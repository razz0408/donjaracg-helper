<script>
  import { ref } from 'vue';
  import CuteIdols from "../assets/idol_data_cute.json";
  import CoolIdols from "../assets/idol_data_cool.json";
  import PassionIdols from "../assets/idol_data_passion.json";

  import draggable from "vuedraggable";

  export default {
    components: {
        draggable,
    },
    data() {
      return {
        keyword: '',
        idolData: [...CuteIdols.idol_data, ...CoolIdols.idol_data, ...PassionIdols.idol_data],

        targetArray: []
      }
    },
    computed: {
      filteredIdols: function() {
        var idols = [];

        for(var idol of this.idolData) {
          // var idol = this.idolData[i];

          if(idol.furigana.replaceAll(/\s+/g, '').indexOf(this.keyword) !== -1) {
            idols.push(idol);
          }
        }

        for(var selectedIdol of this.targetArray) {
          // var selectedIdol = this.targetArray[j];

          if(idols.includes(selectedIdol)) {
            var index = idols.indexOf(selectedIdol);
            idols.splice(index, 1);
          }
        }

        return idols;
      }
    },
    methods: {
      onDrop() {
        this.$emit('on-drop', this.targetArray);
      }
    }
  }
</script>

<style scoped>
.drag-item {
  background: rgb(233, 249, 255);
  margin: 10px 0;
}
</style>

<template>  
  <div class="container">
    <div class="container-title">
      <p>アイドル一覧</p>
    </div>

    <div class="filter">
      <p class="pc-only">アイドルの名前で絞り込む (ひらがな) ※部分一致</p>
      <p class="sp-only">アイドルの名前で絞り込む (ひらがな)</p>
      <input type="text" v-model="keyword">
    </div>

    <draggable v-model="filteredIdols" group="people" item-key="cgid" class="component component-idols">
      <template #item="{element}">
        <div class="idol-card">
          <div class="idol-detail" :data-type="element.type">
            <div class="idol-petit">
              <img :src="'../images/' + element.image" width="140" height="190" >
            </div>

            <p class="idol-furigana">{{ element.furigana }}</p>
            <p class="idol-name">{{ element.name }}</p>
          </div>
        </div>
      </template>
    </draggable>
  </div>

  <div class="container">
    <div class="container-title">
      <p>手牌にいるアイドル</p>
    </div>

    <draggable v-model="targetArray" group="people" item-key="cgid" class="component component-idols" @change="onDrop">
      <template #item="{element}">
        <div class="idol-card">
          <div class="idol-detail" :data-type="element.type">
            <div class="idol-petit">
              <img :src="'../images/' + element.image" width="140" height="190" >
            </div>

            <p class="idol-furigana">{{ element.furigana }}</p>
            <p class="idol-name">{{ element.name }}</p>
          </div>
        </div>
      </template>
    </draggable>
  </div>

  <!-- <hr>
  <div>
    {{ targetArray }}
  </div> -->
</template>

