<template>
    <input type="text" @input="onInput" :value="modelValue" >
    <input :value="other" @input="onOtherInput" />
</template>

<script>
export default {
  props: {
    modelValue: {
        type: String,
        default: ''
    },
    other: {
        type: String,
        default: ''
    }
  },
  methods:{
    onInput(e){
        console.log(e.target.value)
        this.$emit(e.target.value)
    },
    onOtherInput(e){
        console.log(e.target.value)
        this.$emit('update:other', e.target.value)
    }
  }
}
</script>