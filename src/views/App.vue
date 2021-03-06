<template>
  <div id="app">
    <vue-headful :title="title" description="Nertivia Chat Client"/>
    <div class="background-image"></div>
    <transition name="fade-between-two" appear>
      <ConnectingScreen v-if="!loggedIn"/>
      <div class="box" v-if="loggedIn">
        <div class="frame">
          <div class="tabs">
            <div :class="`tab ${currentTab === 0 ? 'selected' : ''}`" @click="switchTab(0)">
              <i class="material-icons">list_alt</i>
              Changelog
            </div>

            <div :class="`tab ${currentTab === 1 ? 'selected' : ''}`" @click="switchTab(1)">
              <i class="material-icons">chat</i>
              Direct Message
            </div>

            <div :class="`tab ${currentTab === 2 ? 'selected' : ''}`" @click="switchTab(2)">
              <i class="material-icons">forum</i>
              Servers
            </div>
            <div :class="`tab ${currentTab === 3 ? 'selected' : ''}`" @click="switchTab(3)">
              <i class="material-icons">rss_feed</i>
              Server Browser
            </div>
          </div>
          <div class="drag-area" v-if="isElectron"></div>
          <electron-frame-buttons v-if="isElectron"/>
        </div>
        <div class="panel-layout">
          <news v-if="currentTab == 0"/>
          <direct-message v-if="currentTab == 1"/>
          <!-- <servers v-if="currentTab == 2"/> -->
          <div class="coming-soon" v-if="currentTab > 1">
            <div class="icon">
              <i class="material-icons">cached</i>
            </div>
            <div class="text">Coming soon!</div>
          </div>
        </div>
      </div>
    </transition>
    <Popouts v-if="loggedIn"/>
  </div>
</template>

<script>
import { bus } from "../main";
import Popouts from "@/components/app/Popouts/Popouts.vue";

import changelog from "@/utils/changelog.js";
import ConnectingScreen from "./../components/app/ConnectingScreen.vue";
import Spinner from "./../components/Spinner.vue";

const ElectronFrameButtons = () =>
  import("./../components/ElectronJS/FrameButtons.vue");
  
const News = () => import(/* webpackChunkName: "News" */"./../components/app/Tabs/News.vue");
//const DirectMessage = () => import('./../components/app/Tabs/DirectMessage.vue');
const DirectMessage = () => ({
  component: import("./../components/app/Tabs/DirectMessage.vue"),
  loading: Spinner,
  delay: 0
});
const Servers = () => ({
  component: import("./../components/app/Tabs/Servers.vue"),
  loading: Spinner,
  delay: 0
});

export default {
  name: "app",
  components: {
    ElectronFrameButtons,
    DirectMessage,
    Servers,
    ConnectingScreen,
    Popouts,
    News
  },
  data() {
    return {
      currentTab: localStorage.getItem("currentTab") || 0,
      title: "Nertivia",
      isElectron: window && window.process && window.process.type
    };
  },
  methods: {
    switchTab(index) {
      localStorage.setItem("currentTab", index);
      this.currentTab = index;
    }
  },
  mounted() {
    // check if changelog is updated
    const seenVersion = localStorage.getItem("changelog-version-seen");
    if (!seenVersion || seenVersion < changelog[0].version) {
      this.currentTab = 0;
      localStorage.setItem("currentTab", 0);
    }
    localStorage.setItem("changelog-version-seen", changelog[0].version);
    bus.$on("title:change", title => {
      this.title = title;
    });
  },
  computed: {
    loggedIn() {
      return this.$store.getters.loggedIn;
    }
  }
};
</script>


<style scoped>
#app {
  position: fixed;
  width: 100%;
  height: 100%;
}
.coming-soon {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.39);
  color: white;
}

.coming-soon .material-icons {
  font-size: 100px;
}

.direct-message-tab {
  position: relative;
  display: flex;
  width: 100%;
  height: 100%;
}

.frame {
  display: flex;
  -webkit-app-region: drag;
  flex-shrink: 0;
}

.drag-area {
  display: flex;
  min-width: 20px;
  flex: 1;
  -webkit-app-region: drag;
}

.tabs {
  display: flex;
  overflow-y: hidden;
  overflow-x: auto;
  height: 35px;
  max-width: 479px;
  flex-basis: auto; /* default value */
  flex-grow: 1;
  -webkit-app-region: no-drag;
}

.tabs::-webkit-scrollbar {
  height: 5px;
}

.tab {
  flex-shrink: 0;
  margin: auto;
  margin-right: 1px;
  margin-left: 3px;
  margin-bottom: 0;
  background: rgba(0, 0, 0, 0.63);
  color: white;
  padding: 5px;
  border-top-right-radius: 5px;
  border-top-left-radius: 5px;
  cursor: default;
  user-select: none;
  transition: 0.3s;
  -webkit-app-region: no-drag;
}
.tab.selected {
  background: rgba(71, 71, 71, 0.637);
}

.tab:hover {
  background: rgba(71, 71, 71, 0.637);
}

.tab .material-icons {
  font-size: 15px;
  vertical-align: -2px;
}

.slidein-enter-active,
.slidein-leave-active {
  transition: 0.5s;
}
.slidein-enter, .slidein-leave-to /* .fade-leave-active below version 2.1.8 */ {
  margin-left: -300px;
}

.fade-between-two-enter-active,
.fade-between-two-leave-active {
  transition: 0.3s;
}
.fade-between-two-enter,
.fade-between-two-leave-to {
  opacity: 0 !important;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
@media (max-width: 470px) {
  .tabs {
    height: 40px;
  }
}
</style>



<style>

textarea {
  font-family: "Roboto", sans-serif;
}
.box {
  display: flex;
  flex-direction: column;
  height: 100%;
  width: 100%;
}
.background-image {
  background: url(./../assets/background.jpg);
  position: fixed;
  z-index: -1;
  width: 100%;
  height: 100%;
  background-repeat: no-repeat;
  background-position: bottom;
  background-size: cover;
}

.panel-layout {
  display: flex;
  overflow: auto;
  height: 100%;
  border-top-left-radius: 5px;
  border-top-right-radius: 5px;
}


input{
  padding: 10px;
  background: rgba(0, 0, 0, 0.301);
  outline: none;
  border: none;
  color: white;
  margin-top: 5px;
  margin-bottom: 10px;
  width: 200px;
  transition: 0.3s;
}

input:hover{
  background: rgba(0, 0, 0, 0.452);
}

input:focus {
  background: rgba(0, 0, 0, 0.603);
}

</style>

