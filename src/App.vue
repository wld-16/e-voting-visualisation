<template>
  <h1>Network Visualization</h1>
  <hello-world></hello-world>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue"

export default {
  name: 'App',
  components: {
    HelloWorld
  },
  data() {
    return {
      connection: null
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
