<template>
  <form class="q-form" @submit="submit" @reset="reset">
    <slot></slot>
  </form>
</template>

<script>
  import { singularize } from 'inflection';
  import { get, set, merge, cloneDeep, isEqualWith } from 'lodash';
  import { equalModelValues } from '../utils/strings';
  import { stopAndPrevent } from '../utils/events';

  import models from 'src/models';

  export default {
    name: 'DbForm',
    provide() {
      return {
        form: this
      }
    },
    props: {
      value: {
        type: Object,
        required: true
      },
      initialValue: {
        type: Object,
        required: true
      },
      modelName: String,
      isDirty: Boolean,
      extraValidations: {
        type: Function,
        default: () => ({})
      }
    },
    computed: {
      model() {
        return models[this.modelName];
      },
      canLeave() {
        return !this.notChanged;
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
        loadedValue: {}
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
          });
        }
        this.model && iterator(this.model.fields);
        return validations;
      },
      submit(evt) {
        evt && stopAndPrevent(evt);
        this.$v.$touch();
        !this.$v.$error && this.$emit('submit', cloneDeep(this.value));
      },
      reset(evt) {
        evt && stopAndPrevent(evt);
        this.$emit('update:value', cloneDeep(this.initialValue));
        this.$v.$reset();
        this.$emit('reset', this.initialValue);
      },
      cancel(evt) {
        evt && stopAndPrevent(evt);
        this.$emit('update:value', cloneDeep(this.loadedValue));
        this.$v.$reset();
        this.$emit('cancel', this.loadedValue);
      }
    },
    validations() {
      return this.model ? {
        value: merge(this.getValidationsRecursively(), this.extraValidations())
      } : {};
    },
    mounted() {
      this.$emit('update:value', merge({}, this.initialValue, this.value));
      this.loadedValue = cloneDeep(this.value);
      this.isDirty && this.$v.$touch();
    }
  }
</script>
