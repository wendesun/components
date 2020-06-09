<template>
  <div id="app">
    <MyAudio/>
  </div>
</template>

<script>

import MyAudio from './components/my-audio.vue'

export default {
  name: 'App',
  components: {
    MyAudio
  },
  data(){
    return{
      url : ""
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
