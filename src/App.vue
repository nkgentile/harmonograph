<template>
  <div id="app">
    <Harmonograph
      :frames="frames"
    />
  </div>
</template>

<script>
import Harmonograph from './components/Harmonograph.vue'

export default {
  name: 'app',
  components: {
    Harmonograph
  },
  data(){
    return {
      frames: [],
      counter: 0,
    };
  },

  mounted(){
    setInterval(this.addFrame, 100);
  },

  methods: {
    addFrame(){
      this.frames = [
        ...this.frames,
        this.counter++,
      ];
    },
  },
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
