<template>
  <q-form @submit="submit" @reset="reset" v-bind="$props">
    <slot></slot>
  </q-form>
</template>

<script>

  import deepKeys from 'deep-keys';
  import { singularize } from 'inflection';
  import { get, set, merge, cloneDeep, isEqualWith, pick, isEmpty } from 'lodash';
  import { equalModelValues } from '../utils/strings';
  import { stopAndPrevent } from '../utils/events';
  import * as models from 'src/models';

  export default {
    name: 'DbForm',
    provide() {
      return {
        form: this
      }
    },
    props: {
      autofocus: Boolean,
      beforeSubmit: Function,
      value: {
        type: Object,
        required: true
      },
      initialValue: {
        type: Object,
        default: () => ({})
      },
      modelName: {
        type: String,
        required: true
      },
      isDirty: Boolean,
      extraValidations: {
        type: Object,
        default: () => ({})
      },
      saveOnSubmit: Boolean
    },
    computed: {
      model() {
        return models[this.modelName];
      },
      canLeave() {
        return this.notChanged;
      },
      notChanged() {
        return isEqualWith(this.value, this.loadedValue, equalModelValues);
      },
      isEmpty() {
        return isEqualWith(this.value, this.initialValue, equalModelValues);
      },
      hasErrors() {
        return this.$v.$error;
      }
    },
    data() {
      return {
        loadedValue: {},
        validationValue: {}
      }
    },
    methods: {
      hasPath(path) {
        if (get(this.value, path) !== undefined) {
          return true;
        } else {
          const arr = path.split('.$each.');
          const arrName = arr.shift();
          const arrValue = get(this.value, arrName);
          return Array.isArray(arrValue)
              ? arrValue.length === 0 || arrValue.some((el, index) => this.hasPath(`${arrName}[${index}].${arr.join('.$each.')}`))
              : false;
        }
      },
      getValidationsRecursively() {
        let validations = {};
        const iterator = (obj, prevKey) => {
          Object.keys(obj).forEach(key => {
            const field = cloneDeep(obj[key]);
            const newKey = prevKey ? `${prevKey}.${key}` : key;
            if (field.validate && typeof field.validate === 'object' && this.hasPath(newKey)) {
              set(validations, newKey, field.validate);
            }
            if (field.type === 'model' && ['hasMany', 'hasOne'].includes(field.relation)) {
              const modelObj = models[field.relation === 'hasMany' ? singularize(key) : key];
              modelObj && iterator(modelObj.fields, field.relation === 'hasMany' ? `${newKey}.$each` : newKey);
            }
            if (field.type === undefined && typeof field === 'object') {
              iterator(field, newKey);
            }
          });
        }
        this.model && iterator(this.model.fields);
        return validations;
      },
      async save() {
        try {
          this.$q.loading.show({message: 'Збереження...'});
          const data = await this.$api.model.save(this.modelName, this.value);
          this.loadedValue = isEmpty(this.initialValue) ? merge({}, this.value, data) : pick(data, deepKeys(this.initialValue));
          this.$q.notify({color: 'positive', timeout: 2500, message: 'Дані успішно збережно', position: 'top', icon: 'done'});
          this.$emit('update:value', cloneDeep(this.loadedValue));
          this.$emit('save', cloneDeep(this.loadedValue));
        } catch (err) {
          console.error(err);
        } finally {
          this.$q.loading.hide();
        }
      },
      async submit(evt) {
        evt && stopAndPrevent(evt);
        this.$v.$touch();
        if (this.$v.$error) {
          return false;
        }
	this.$v.$reset();
        this.beforeSubmit && await this.beforeSubmit(this.value);
        this.$emit('submit');
        this.saveOnSubmit && await this.save();
      },
      reset(evt) {
        evt && stopAndPrevent(evt);
        this.$emit('update:value', cloneDeep(this.initialValue));
        this.$v.$reset();
        this.$emit('reset');
      },
      cancel(evt) {
        evt && stopAndPrevent(evt);
        this.$emit('update:value', cloneDeep(this.loadedValue));
        this.$v.$reset();
        this.$emit('cancel');
      }
    },
    validations() {
      return {value: this.validationValue};
    },
    async mounted() {
      this.$emit('update:value', merge({}, this.initialValue, this.value));
      await this.$nextTick();
      this.model && (this.validationValue = merge({}, this.getValidationsRecursively(), get(this.extraValidations, this.modelName)));
      this.isDirty && this.$v.$touch();
      this.loadedValue = cloneDeep(this.value);
    }
  }
</script>
