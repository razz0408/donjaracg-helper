
<script setup>
  const props = defineProps(['selectedIdols'])
</script>

<script>
  import { ref } from 'vue';
  import UnitData from "../assets/unit_data.json";
  import CuteIdols from "../assets/idol_data_cute.json";
  import CoolIdols from "../assets/idol_data_cool.json";
  import PassionIdols from "../assets/idol_data_passion.json";

  export default {
    // props: ['selectedIdols'],
    data() {
      return {
        unitData: UnitData.unit_data,
        idolData: [...CuteIdols.idol_data, ...CoolIdols.idol_data, ...PassionIdols.idol_data],
      }
    },
    computed: {
      filteredUnits: function() {
        var units = [];
        var idols = [];

        if(!this.selectedIdols.length) {
          return this.unitData;
        }

        for(var idol of this.selectedIdols) {
          idols.push(idol.name);
        }

        for(var unit of this.unitData) {
          let result = idols.every(val => unitCheck(val));

          function unitCheck(val) {
            return unit.members.includes(val);
          }

          if(result) {
            units.push(unit);
          }
        }

        return units;
      }
    },
    methods: {
      getIdolsByNames: function(nameArray) {
        var idols = [];

        for(var idolName of nameArray) {
          for(var idol of this.idolData) {
            if(idol.name === idolName) {
              idols.push(idol);
              continue;
            }
          }
        }

        return idols;
      }
    }
  }
</script>

<template>
  <div class="container">
    <div class="container-title">
      <p>ユニット一覧</p>

      <div class="component">
        <div class="unit-card" v-for="(unit, key) in filteredUnits" :key="key">
          <div class="unit-detail">
            <p class="unit-furigana">{{ unit.yomi }}</p>
            <p class="unit-name">{{ unit.unit_name }}</p>
            <p class="song-unit" v-if="unit.song">※楽曲ユニット</p>
          </div>
          <div class="unit-members">
            <div class="idol-card" v-for="(idol, key) in getIdolsByNames(unit.members.split(','))" :key="key">
              <div class="idol-detail" :data-type="idol.type">
                <div class="idol-petit">
                  <img :src="'../images/' + idol.image" width="140" height="190" >
                </div>

                <p class="idol-furigana">{{ idol.furigana }}</p>
                <p class="idol-name">{{ idol.name }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

</template>
