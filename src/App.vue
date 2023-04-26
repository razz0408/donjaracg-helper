<script setup>
</script>

<script>
import TabSearchUnit from './components/tabs/TabSearchUnit.vue';
import TabPointCalculator from './components/tabs/TabPointCalculator.vue';

import FooterModule from './components/modules/Footer.vue'

export default {
  components: {
    TabSearchUnit, TabPointCalculator, FooterModule
  },
  data() {
    return {
      currentTab: 'TabSearchUnit',
    }
  }
}
</script>

<template>
  <h1>ドンジャラヘルパー (仮)</h1>

  <hr>

  <div class="main-wrapper">
    <ul class="tab-menu">
      <li :class="{ active: currentTab === 'TabSearchUnit' }" @click="currentTab = 'TabSearchUnit'">ユニット検索</li>
      <li :class="{ active: currentTab === 'TabPointCalculator' }" @click="currentTab = 'TabPointCalculator'">点数計算 (WIP)</li>
    </ul>
    <div class="tab-content">
      <KeepAlive>
        <component :is="currentTab"></component>
      </KeepAlive>
    </div>
  </div>

  <FooterModule />

</template>

<style lang="scss">
h1 {
  font-size: 30px;
}

a {
  color: #0066c0;
  &:hover {
    color: #0080ff;
  }
}

#app {
  width: 100%;
  margin: 0 auto;
  text-align: center;

  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  box-sizing: border-box;
}

// PC
@media screen and (min-width: 769px) {
  .sp-only {
    display: none;
  }

  #app {
    max-width: 1280px;

    padding-left: 15px;
    padding-right: 15px;
  }

  .main-wrapper {
    .tab-menu {
      display: flex;
      margin: 0;
      padding: 0;
      list-style-type: none;

      li {
        display: block;
        margin-right: 8px;
        padding: 10px 20px;
        border-style: solid;
        border-width: 1px;
        border-color: transparent;
        border-radius: 4px 4px 0 0;
        color: #557f95;
        text-decoration: none;
      }

      .active {
        // border-color: #1e6ba7 #1e6ba7 transparent #1e6ba7;
        background-color: rgba(#2681c8, 0.5);
        color: black;
        font-weight: 600;
      }
      :not(.active) {
        background-color: rgba(#29597e, 0.5);
        color: black;
        font-weight: 600;
      }
    }

    .tab-content {
      background-color: rgba(#2681c8, 0.5);
      border-radius: 0 15px 15px 15px;

      padding-top: 10px;
      padding-left: 10px;
      padding-right: 10px;
      padding-bottom: 10px;

      .tab-title {
        .text {
          color: black;
          font-size: 20px;
          font-weight: 600;
        }
      }
    }
  }

  .module-wrapper {
    background-color: rgba(#fff, 0.3);
    border-radius: 10px;

    +.module-wrapper {
      margin-top: 20px;
    }
  }

}

// SP
@media screen and (max-width: 768px) {
  .pc-only {
    display: none;
  }

  #app {
    padding: 1rem;
  }

  .main-wrapper {
    .tab-menu {
      display: flex;
      margin: 0;
      padding: 0;
      list-style-type: none;

      li {
        display: block;
        margin-right: 8px;
        padding: 10px 20px;
        border-style: solid;
        border-width: 1px;
        border-color: transparent;
        border-radius: 4px 4px 0 0;
        color: #557f95;
        text-decoration: none;
      }

      .active {
        // border-color: #1e6ba7 #1e6ba7 transparent #1e6ba7;
        background-color: rgba(#2681c8, 0.5);
        color: black;
        font-weight: 600;
      }
      :not(.active) {
        background-color: rgba(#29597e, 0.5);
        color: black;
        font-weight: 600;
      }
    }

    .tab-content {
      background-color: rgba(#2681c8, 0.5);
      border-radius: 0 15px 15px 15px;

      padding-top: 10px;
      padding-left: 10px;
      padding-right: 10px;
      padding-bottom: 10px;

      .tab-title {
        .text {
          color: black;
          font-size: 20px;
          font-weight: 600;
        }
      }
    }
  }

  .module-wrapper {
    background-color: rgba(#fff, 0.3);
    border-radius: 10px;

    +.module-wrapper {
      margin-top: 20px;
    }
  }
}
</style>

<style lang="scss">
// スクロールバー
// * {
//   &::-webkit-scrollbar {
//     border-radius: 10px;
//     width: 16px;
//     // background-color: #F5F5F5;
//   }
//   &::-webkit-scrollbar-thumb {
//     border-radius: 10px;
//     // -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,.3);
//     background-color: rgb(114, 113, 113);
    
//     background-clip: padding-box;
//   }
//   &::-webkit-scrollbar-track {
//     // -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,0.3);
//     border-radius: 10px;
//     background-color: #F5F5F5;
//   }
// }
</style>