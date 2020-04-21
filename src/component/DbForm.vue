<template>
  <q-form @submit="submit" @reset="reset" v-bind="$props">
    <slot></slot>
  </q-form>
</template>

<script>

  import deepKeys from 'deep-keys';
  import { singularize } from 'inflection';
  import { get, set, merge, cloneDeep, isEqualWith, pick, isEmpty, filter } from 'lodash';
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
      //isDirty: Boolean,
      //extraValidations: {
      //  type: Object,
      //  default: () => ({})
      //},
      saveOnSubmit: Boolean
    },
    data() {
      return {
        [this.value.$options.name]: this.value
      }
    },
    methods: {
      /*
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
       },*/
      async submit(evt) {

        evt && stopAndPrevent(evt);
        this.value.$validate.$touch();

        if (this.value.$validate.$error) {
          return false;
        }
        console.log('SUBMIT');
        this.value.$validate.$reset();
        this.beforeSubmit && await this.beforeSubmit(this.value);
        await this.value.$commit(this.saveOnSubmit);
        this.$emit('submit');
      },
      reset(evt) {
        console.log('FORM reset');
        return;
        evt && stopAndPrevent(evt)

        this.value.$reset();
        this.$emit('reset');
      },

      cancel(evt) {
        return;
        console.log('FORM cancel');
        evt && stopAndPrevent(evt);

        this.value.$rollback();
        this.$emit('cancel');
      }
    },
    /*
     validations() {
     return {[this.value.$options.name]: this.value.$validate};
     },*/
    async mounted() {
      // console.log(this.value);
      //this.$emit('update:value', merge({}, this.initialValue, this.value));
      //await this.$nextTick();
      //this.model && (this.validationValue = merge({}, this.getValidationsRecursively(), get(this.extraValidations, this.modelName)));
      //this.isDirty && this.$v.$touch();
      //this.loadedValue = cloneDeep(this.value);
      // this.validationValue = merge({}, this.getValidationsRecursively(), get(this.extraValidations, this.value.$options.name))
    },
    destroyed() {
     // this.value.$destroy();
    }
  }
</script>
