<template>
  <div id="app">
    <img class="logo" alt="Vue logo" src="./assets/logo.svg">
    <Hello msg="Visual Validation Service"/>
  </div>
</template>

<script>
import Hello from './components/Hello.vue'

export default {
  name: 'app',
  components: {
    Hello
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.logo{
  max-height: 78px;
}
</style>
