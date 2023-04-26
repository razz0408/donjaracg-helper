<script>
import ProduceIdol from '../modules/ProduceIdol.vue';
import WaitIdols from '../modules/WaitIdols.vue';
import PointCalculator from '../modules/PointCalculator.vue';

export default {
  components: {
    ProduceIdol, WaitIdols, PointCalculator
  },
  data() {
    return {
      produceIdol: {},
      handIdols: []
    }
  },
  methods: {
    setProduceIdol(idol) {
      this.produceIdol = idol;
    },
    getHandIdols($event) {
      this.handIdols = $event;
    }
  }
}
</script>

<template>
  <div class="tab-title">
    <p class="text">ファン人数計算</p>
  </div>
  
  <ProduceIdol @add="setProduceIdol" />
  <WaitIdols v-on:on-drop="getHandIdols" />
  <PointCalculator :produceIdol=produceIdol :handIdols=handIdols />
  
</template>