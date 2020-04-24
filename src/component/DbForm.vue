<template>
  <q-form @submit="submit" @reset="reset" v-bind="$attrs" v-on="$listeners">
    <slot></slot>
  </q-form>
</template>

<script>

  const stopAndPrevent = (e) => {
    e.cancelable !== false && e.preventDefault();
    e.stopPropagation();
  }

  export default {
    name: 'DbForm',
    provide() {
      return {
        form: this
      }
    },
    props: {
      beforeSubmit: Function,
      value: {
        type: Object,
        required: true
      },
      saveOnSubmit: Boolean
    },
    data() {
      return {
        [this.value.$options.name]: this.value
      }
    },
    methods: {
      async submit(evt) {
        evt && stopAndPrevent(evt);
        this.value.$validate.$touch();

        console.log('SUBMIT 1', this.value.$errors, this.value.$validate);
        if (this.value.$validate.$error) {
          return false;
        }
        console.log('SUBMIT 2');
        //return;
        this.value.$validate.$reset();
        this.beforeSubmit && await this.beforeSubmit(this.value);
        await this.value.$commit(this.saveOnSubmit);
        this.$emit('submit');
      },
      reset(evt) {
        evt && stopAndPrevent(evt)
        this.value.$reset();
        this.$emit('reset');
      },
      cancel(evt) {
        evt && stopAndPrevent(evt);
        this.value.$rollback();
        this.$emit('cancel');
      }
    },
    beforeDestroy() {
      this.value.$destroy();
    }
  }
</script>
