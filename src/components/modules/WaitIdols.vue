<script>
  import { ref } from 'vue';
  import CuteIdols from "../../assets/idol_data_cute.json";
  import CoolIdols from "../../assets/idol_data_cool.json";
  import PassionIdols from "../../assets/idol_data_passion.json";
  import AlmightyIdols from "../../assets/idol_data_almighty.json";

  import draggable from "vuedraggable";

  export default {
    components: {
        draggable,
    },
    data() {
      return {
        keyword: '',
        // idolData: [...AlmightyIdols.idol_data, ...CuteIdols.idol_data, ...CoolIdols.idol_data, ...PassionIdols.idol_data],
        idolData: [...CuteIdols.idol_data, ...CoolIdols.idol_data, ...PassionIdols.idol_data],

        targetArray: []
      }
    },
    computed: {
      filteredIdols: function() {
        var idols = [];

        for(var idol of this.idolData) {
          if(idol.furigana.replaceAll(/\s+/g, '').indexOf(this.keyword) !== -1) {
            idols.push(idol);
          }
        }

        // for(var selectedIdol of this.targetArray) {
        //   if(idols.includes(selectedIdol)) {
        //     var index = idols.indexOf(selectedIdol);
        //     idols.splice(index, 1);
        //   }
        // }

        return idols;
      }
    },
    methods: {
      onDrop() {
        this.$emit('on-drop', this.targetArray);
      },
      checkMove() {
        if(this.targetArray.length == 9) {
          return false;
        } else {
          return true;
        }
      }
    }
  }
</script>

<template>  
  <div class="module-wrapper">
    <div class="container">
      <div class="container-title">
        <p>アイドル一覧</p>
      </div>

      <div class="filter">
        <p class="pc-only">アイドルの名前で絞り込む (ひらがな) ※部分一致</p>
        <p class="sp-only">アイドルの名前で絞り込む (ひらがな)</p>
        <input type="text" v-model="keyword">
      </div>

      <draggable v-model="filteredIdols" group="people" item-key="cgid" class="component" :move="checkMove">
        <template #item="{element}">
          <div class="idol-card">
            <div class="idol-detail" :data-type="element.type">
              <div class="idol-image">
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
        <p>点数を知りたい手牌</p>
      </div>

      <draggable v-model="targetArray" group="people" item-key="cgid" class="component" @change="onDrop">
        <template #item="{element}">
          <div class="idol-card">
            <div class="idol-detail" :data-type="element.type">
              <div class="idol-image">
                <img :src="'../images/' + element.image" width="140" height="190" >
              </div>

              <p class="idol-furigana">{{ element.furigana }}</p>
              <p class="idol-name">{{ element.name }}</p>
            </div>
          </div>
        </template>
      </draggable>
    </div>
  </div>
</template>

<style lang="scss">
// 共通 - 各タイプ毎のカラー
.component {
  .idol-card {
    [data-type="cute"] {
      // background-color: rgba(#ff0073, 1.0);
      background-color: #ef7fb2;
    }
    
    [data-type="cool"] {
      // background-color: rgba(#006aff, 1.0);
      background-color: #5da1ff;
    }
    
    [data-type="passion"] {
      // background-color: rgba(#ffaa00, 1.0);
      background-color: #ffc249;
    }
  }
}

// 共通
.module-wrapper {
  .container {

  }
}

.component {
  .idol-card {
    &:first-child {
      margin-left: auto;
    }
    &:last-child {
      margin-right: auto;
    }
    .idol-detail {
      display: flex;
      flex-direction: column;
      justify-content: flex-end;
      align-items: center;
      
      .idol-image {
        img {
          min-width: 100px;
          width: 100%;
          height: 100%;
          
          user-drag: none;
          -webkit-user-drag: none;
          -moz-user-select: none;
        }
      }
      
      .idol-furigana {
        color: rgba(#111, 0.7);
        margin: 0;
      }

      .idol-name {
        color: rgba(#111, 0.9);
        margin: 0;
      }
    }
  }
}

// PC
@media screen and (min-width: 769px) { 
  .module-wrapper {
    .container {
    }
  }
}

// SP
@media screen and (max-width: 768px) {
  .module-wrapper {
    .container {
    }
  }
}
</style>

<style lang="scss" scoped>
.component {
  padding: 5px;
  border-radius: 10px;
  background-color: rgba(#2681c8, 0.5);
  
  display: flex;
  overflow-x: auto;

  .idol-card {
    user-select: none;
  }
}

// PC
@media screen and (min-width: 769px) { 
  .filter {
    display: flex;
    flex-direction: column;
    align-items: center;

    border-radius: 10px;

    p {
      text-align: left;
      margin: 0;
      font-weight: 600;
      color: rgb(0, 0, 0);
    }

    input {
      background-color: #fff;

      width: 40%;
      border-radius: 10px;

      margin-top: 5px;
      padding-left: 10px;
      padding-right: 10px;
    }
  }
  .component {
    min-height: 220px;
    gap: 10px;

    .idol-card {
      min-width: 110px;
      max-width: 110px;

      cursor: pointer;

      .idol-detail {
        padding-top: 5px;
        padding-left: 5px;
        padding-right: 5px;
        padding-bottom: 5px;

        border-radius: 12px;
        
        .idol-furigana {
          font-size: 10px;
          font-weight: 600;
        }

        .idol-name {
          font-size: 14px;
          font-weight: 600;
        }
      }
    }
  }
}

// SP
@media screen and (max-width: 768px) {
  .filter {
    display: flex;
    flex-direction: column;
    align-items: center;

    border-radius: 10px;

    p {
      text-align: left;
      margin: 0;
      font-weight: 600;
      color: rgb(0, 0, 0);
    }

    input {
      background-color: #fff;
      width: 90%;
      border-radius: 10px;
      margin-top: 5px;
      padding-left: 10px;
      padding-right: 10px;
    }
  }

  .component {
    min-height: 240px;
    gap: 30px;
    
    .idol-card {
      min-width: 130px;
      max-width: 130px;

      .idol-detail {
        padding-top: 5px;
        padding-left: 5px;
        padding-right: 5px;
        padding-bottom: 5px;
        
        border-radius: 12px;

        .idol-furigana {
          font-size: 12px;
        }

        .idol-name {
          font-size: 16px;
        }
      }
    }
  }
}
</style>