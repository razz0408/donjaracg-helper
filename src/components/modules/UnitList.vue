
<script setup>
  const props = defineProps(['selectedIdols'])
</script>

<script>
  import UnitData from "../../assets/unit_data.json";
  import CuteIdols from "../../assets/idol_data_cute.json";
  import CoolIdols from "../../assets/idol_data_cool.json";
  import PassionIdols from "../../assets/idol_data_passion.json";

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
          return this.unitData.filter(function(val){
            // 8人ユニットは和了できるパターンが謎いので除外
            return val.members.split(',').length != 8;
          });
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
  <div class="module-wrapper">
    <div class="container">
      <div class="container-title">
        <p class="text">ユニット一覧</p>
      </div>

      <div class="component">
        <div class="unit-info" v-for="(unit, key) in filteredUnits" :key="key">
          <div class="unit-detail">
            <p class="unit-furigana">{{ unit.yomi }}</p>
            <p class="unit-name">{{ unit.unit_name }}</p>
            <p class="song-unit" v-if="unit.song">※楽曲ユニット</p>
          </div>
          <div class="unit-members">
            <div class="idol-card" v-for="(idol, key) in getIdolsByNames(unit.members.split(','))" :key="key">
              <div class="idol-detail" :data-type="idol.type">
                <div class="idol-image">
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

<style lang="scss">
// 共通
.module-wrapper {
  .container {

  }
}

.component {
  .unit-info {
    display: flex;
    flex-direction: column;

    .unit-detail {
      margin-left: auto;
      margin-right: auto;
      margin-bottom: 5px;

      width: fit-content;

      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;

      padding-top: 3px;
      padding-bottom: 3px;
      padding-left: 10px;
      padding-right: 10px;

      border-radius: 10px;

      background-color: rgba(#ffffff, 0.3);

      p {
        width: fit-content;
      }
      
      .unit-furigana {
        color: rgba(#111, 0.7);
        font-weight: 600;
      }

      .unit-name {
        color: rgba(#111, 0.9);
        font-weight: 600;
      }

      .song-unit {
        color: rgba(#111, 0.7);
      }
    }

    .unit-members {
      padding: 5px;
      border-radius: 10px;
      background-color: rgba(#225cad, 0.5);

      .idol-card {
        .idol-detail {
          border-radius: 10px;
          
          .idol-furigana {
            // margin-top: 5px;
            font-weight: 600;
          }

          .idol-name {
            font-weight: 600;
          }
        }
      }
    }
  }
}

// PC
@media screen and (min-width: 769px) {
  .component {
    .unit-info {
      +.unit-info {
        margin-top: 10px;
      }

      .unit-detail {
        // width: 25%;

        .unit-furigana {
          font-size: 13px;
        }

        .unit-name {
          font-size: 17px;
        }

        .song-unit {
          font-size: 15px;
        }
      }

      .unit-members {
        display: flex;
        flex-flow: row;
        gap: 5px;
  
        // width: 80%;
        width: 100%;

        flex-wrap: nowrap;
        overflow-x: auto;

        // スクロールバー 消す？
        // scrollbar-width: none;
        // &::-webkit-scrollbar {
        //   display: none;
        // }

        .idol-card {
          min-width: 110px;
          max-width: 110px;

          max-height: 200px;

          &:first-child {
            margin-left: auto;
          }
          &:last-child {
            margin-right: auto;
          }

          .idol-detail {
            padding: 3px;

            .idol-furigana {
              font-size: 10px;
            }

            .idol-name {
              font-size: 13px;
            }
          }
        }
      }

    }
  }
}

// SP
@media screen and (max-width: 768px) {
  .component {
    .unit-info {
      +.unit-info {
        margin-top: 10px;
      }

      .unit-detail {
        .unit-furigana {
          font-size: 13px;
        }

        .unit-name {
          font-size: 17px;
        }

        .song-unit {
          font-size: 15px;
        }
      }

      
      .unit-members {
        display: flex;
        gap: 15px;
        width: 100%;
  
        overflow-x: auto;

        .idol-card {
          min-width: 110px;
          max-width: 110px;

          max-height: 200px;

          &:first-child {
            margin-left: auto;
          }
          &:last-child {
            margin-right: auto;
          }

          .idol-detail {
            padding: 5px;

            .idol-furigana {
              font-size: 10px;
            }

            .idol-name {
              font-size: 13px;
            }
          }
        }
      }
      
    }
  }
}
</style>