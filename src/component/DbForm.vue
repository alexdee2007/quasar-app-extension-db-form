<template>
  <q-form @submit="submit" @reset="reset" :autofocus="autofocus">
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
      saveOnSubmit: Boolean,
      autofocus: Boolean
    },
    data() {
      return {
        [this.value.$options.name]: this.value
      }
    },
    methods: {
      async submit(evt) {
        evt && stopAndPrevent(evt);
        this.value.$validate();

        console.log('SUBMIT 1', this.value);
        if (this.value.$hasErrors) {
          return false;
        }
        console.log('SUBMIT 2');
        //return;
        this.value.$resetValidation();
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
    }
  }
</script>
