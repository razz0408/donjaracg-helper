<script setup>
</script>

<script>
import Idols from './components/Idols.vue';
import Units from './components/Units.vue';

export default {
  components: {
    Idols, Units
  },
  data() {
    return {
      targetArray: []
    }
  },
  methods: {
    getSelectedIdols($event) {
      this.targetArray = $event;
    }
  }
}
</script>

<template>
  <h1>ドンジャラヘルパー (仮)</h1>

  <hr>
  <Idols v-on:on-drop="getSelectedIdols" />
  <Units :selectedIdols=targetArray />
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  box-sizing: border-box;
}
</style>