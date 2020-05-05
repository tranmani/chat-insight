<template>
  <div :class="classVal">
    <h4>{{ h4Val }}</h4>
    <p>{{ pVal }}</p>
  </div>
</template>

<script>
export default {
  name: 'ResultItem',
  props: {
    classVal: {
      type: String,
      default: ''
    },

    h4Val: {
      default: ''
    },

    pVal: {
      type: String,
      default: ''
    }
  }
}
</script>

<style scoped>
h4 {
  font-weight: bold;
  font-size: 50px;
  margin: 0 0 10px
}
p {
  padding: 0 0 25px
}
</style>
