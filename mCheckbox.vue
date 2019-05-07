<template>
    <input
    type="checkbox"
    :checked="checked || parentChecked"
    @click="changeChecked"
    >
</template>

<script>
  export default {
    name: 'Checkbox',
    props: {
      isChecked: {
        type: Boolean,
        default: false
      },
      parentChecked: {
        type: Boolean,
        default: false
      }
    },
    data() {
      return {
        checked: this.isChecked
      }
    },
    components: {},
    methods: {
      changeChecked() {
        this.checked = !this.checked;
        this.$emit('input', this.checked)
      }
    },
    watch: {
      isChecked(){
        this.checked = this.isChecked
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
input{
  width: 1.5rem;
  height: 1.5rem;
  vertical-align: middle;
}
</style>
