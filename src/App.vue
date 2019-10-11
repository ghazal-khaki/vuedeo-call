<template>
  <div id="app">
    <VuedeoCall />
  </div>
</template>

<script>
import VuedeoCall from './components/VuedeoCall'

export default {
  name: 'app',
  components: {
    VuedeoCall
  }
}
</script>

<style lang="scss">
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
