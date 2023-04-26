<script>
  import CuteIdols from "../../assets/idol_data_cute.json";
  import CoolIdols from "../../assets/idol_data_cool.json";
  import PassionIdols from "../../assets/idol_data_passion.json";

  export default {
    components: {
    },
    data() {
      return {
        keyword: '',
        idolData: [...CuteIdols.idol_data, ...CoolIdols.idol_data, ...PassionIdols.idol_data],

        produceIdol: {
          "cgid": 0,
          "name": "選択してください",
          "furigana": "担当アイドルを",
          "voice": "",
          "type": "cool",
          "image": "0000.png",
          "first_status": ""
        },
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

        return idols;
      }
    },
    methods: {
      setProduceIdol: function(idol) {
        this.produceIdol=idol
        this.$emit("add",this.produceIdol); 
      }
    }
  }
</script>

<template>
  <div class="module-wrapper">
    <div class="container">
      <div class="container-title">
        <p class="text">担当アイドル選択</p>
      </div>

      <div class="produce-idol">
        <div class="text">担当アイドル</div>
        <div class="component">
          <template v-if="produceIdol">
            <div class="idol-card">
              <div class="idol-detail" :data-type="produceIdol.type">
                <div class="idol-image">
                  <img :src="'../images/' + produceIdol.image" width="140" height="190" v-if="produceIdol != undefined">
                </div>

                <p class="idol-furigana">{{ produceIdol.furigana }}</p>
                <p class="idol-name">{{ produceIdol.name }}</p>
              </div>
            </div>
          </template>
        </div>
      </div>
      <div class="idol-list">
        <div class="filter">
          <input type="text" placeholder="アイドルの名前で絞り込む (ひらがな) ※部分一致" v-model="keyword">
        </div>

        <div class="component">
          <div class="idol-card" v-for="(idol, key) in filteredIdols" :key="key" @click="setProduceIdol(idol)">
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
</template>

<style lang="scss">

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

      &::placeholder {
        font-size: 16px;
        color: #aaa
      }
    }
  }

  .module-wrapper {
    .container {
      display: flex;
      flex-wrap: wrap;

      .container-title {
        width: 100%;
      }

      .produce-idol {
        width: 15%;
        display: flex;
        flex-direction: column;
        justify-content: flex-end;
        align-items: center;
        // box-sizing: border-box;
        padding-left: 10px;
        padding-right: 10px;

        .text {
          color: #000;
          font-weight: 600;
        }

        .component {
          display: flex;
          flex-direction: column;

          min-height: 200px;

          .idol-card {
            min-width: none;
            max-width: none;

            .idol-detail {
              min-width: 130px;
              .idol-image {
                max-width: 100px;
              }
            }
          }
        }
      }

      .idol-list {
        width: 85%;
        align-self: flex-end;

        .component {
          min-height: 200px;
        }

        .idol-card {
          cursor: pointer;
        }
      }
    }
  }

  .component {
    gap: 10px;

    .idol-card {
      min-width: 110px;
      max-width: 110px;

      .idol-detail {
        padding: 5px;
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

      &::placeholder {
        font-size: 12px;
        color: #aaa
      }
    }
  }

  .module-wrapper {
    .container {
      margin-left: auto;
      margin-right: auto;
      width: 100%;

      .component {
        width: 100%;

        min-height: 200px;
        gap: 30px;

        .idol-card {
          min-width: 110px;
          max-width: 110px;

          .idol-detail {
            padding-top: 5px;
            padding-left: 5px;
            padding-right: 5px;
            padding-bottom: 5px;
            
            border-radius: 12px;

            .idol-furigana {
              font-size: 10px;
            }

            .idol-name {
              font-size: 14px;
            }
          }
        }
      }

      .produce-idol {
        .component {
          display: flex;
          flex-direction: column;
          justify-content: flex-end;
          align-items: center;

          .idol-card {
            width: 140px;
            max-width: 140px;

            .idol-detail {
              .idol-image {
                max-width: 100px;
              }
            }
          }
        }
      }
    }
  }
}
</style>