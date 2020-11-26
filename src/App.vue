<template>
  <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
  <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->

  <div id="app" style="width: 100%; height: 100%; background: grey; position: relative;">
      <earth-comp></earth-comp>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import EarthComp from './components/EarthComp.vue'

export default {
  name: 'App',
  components: {
    // HelloWorld,
    EarthComp
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
  /* margin-top: 60px; */
}
</style>
