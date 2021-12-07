<template>
  <Hitomezashi />
  <p>Inspired by this video from Numberphile</p><iframe width="560" height="315" src="https://www.youtube.com/embed/JbfhzlMk2eY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</template>

<script>
import Hitomezashi from './components/Hitomezashi.vue'

export default {
  name: 'App',
  components: {
    Hitomezashi
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
