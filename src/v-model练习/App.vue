<template>
  <Child v-model="value" v-model:other="other" />
</template>

<script>
import Child from './v-model/Child.vue'
export default {
  components: {
    Child
  },
  data(){
    return {
      value: '',
      other: ''
    }
  }
}
</script>