<template>
  <div id="app">
    <bitly></bitly>
  </div>
</template>

<script>
import Bitly from './components/Bitly.vue';

export default {
  name: 'app',
  components: {
    Bitly //Sans TableUrls il faut le commenter et utiliser que le composant Bitly.vue
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
</style>
