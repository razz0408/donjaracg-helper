<script>
import IdolList from '../modules/IdolList.vue';
import UnitList from '../modules/UnitList.vue';

export default {
  components: {
    IdolList, UnitList
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
  <div class="tab-title">
    <p class="text">ユニット検索</p>
  </div>
  
  <IdolList v-on:on-drop="getSelectedIdols" />
  <UnitList :selectedIdols=targetArray />
</template>