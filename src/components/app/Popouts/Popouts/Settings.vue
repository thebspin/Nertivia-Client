<template>
  <div class="settings-darken-background">
    <div class="settings-box">
      <div class="tabs">
        <div
          v-for="(tab, index) in tabs"
          :key="index"
          :class="{tab: true, selected: currentTab === index}"
          @click="currentTab = index"
        >
          <div class="material-icons">{{tab.icon}}</div>
          <div class="tab-name">{{tab.name}}</div>
        </div>
      </div>
      <div class="panel">
        <div class="title">
          <div class="material-icons">{{tabs[currentTab].icon}}</div>
          <div class="in-title">{{tabs[currentTab].tabName}}</div>
          <div class="close-button" @click="close">
            <div class="material-icons">close</div>
          </div>
        </div>
        <component :is="tabs[currentTab].component"></component>
      </div>
    </div>
  </div>
</template>

<script>
import { bus } from "@/main";

const MyProfile = () => import("./SettingsPanels/MyProfile.vue");
const ManageEmojis = () => import("./SettingsPanels/ManageEmojis.vue");
const MessageDesign = () => import("./SettingsPanels/MessageDesign.vue");

export default {
  components: {
    MyProfile,
    ManageEmojis,
    MessageDesign
  },
  data() {
    return {
      currentTab: 0,
      tabs: [
        {
          name: "My Profile",
          tabName: "My Profile",
          icon: "account_circle",
          component: "my-profile"
        },
        {
          name: "Message Design",
          tabName: "Message Design",
          icon: "palette",
          component: "message-design"
        },
        {
          name: "Manage Emojis",
          tabName: "Manage Emojis",
          icon: "face",
          component: "manage-emojis"
        }
      ]
    };
  },
  methods: {
    close() {
      this.$store.dispatch("setPopoutVisibility", {
        name: "settings",
        visibility: false
      });
    }
  }
};
</script>


<style scoped>
.settings-darken-background {
  position: absolute;
  background: rgba(0, 0, 0, 0.541);
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 99;
  display: flex;
  color: white;
}
.settings-box {
  height: 600px;
  display: flex;

  margin: auto;
  border-radius: 10px;
  overflow: hidden;
}
.tabs {
  background: rgba(24, 24, 24, 0.938);
  height: 100%;
  width: 200px;
  min-width: 150px;
  overflow-y: auto;
  overflow-x: hidden;
  flex-shrink: 0;
}
.panel {
  display: flex;
  flex-direction: column;
  background: rgba(31, 31, 31, 0.924);
  height: 100%;
  width: 600px;
}
.tab {
  display: flex;
  padding: 10px;
  background: rgba(26, 26, 26, 0.233);
  border-radius: 5px;
  margin-top: 5px;
  margin-bottom: 5px;
  cursor: default;
  user-select: none;
  transition: 0.3s;
  align-items: center;
}
.tab-name {
  margin-left: 10px;
}
.tab.selected {
  background: rgb(61, 61, 61) !important;
}

.tab:hover {
  background: rgba(61, 61, 61, 0.616);
}
.title {
  display: flex;
  padding: 10px;
  font-size: 25px;
  background: rgb(20, 20, 20);
}
.title .material-icons {
  font-size: 40px;
}
.title div {
  margin: auto;
  margin-left: 5px;
  margin-right: 5px;
}
.in-title {
  flex: 1;
}
.close-button {
  display: flex;
  border-radius: 50%;
  padding: 5px;
  cursor: default;
  user-select: none;
  transition: 0.3s;
}
.close-button:hover {
  background: rgba(37, 37, 37, 0.692);
}
.close-button .material-icons {
  margin: auto;
  font-size: 30px;
}

/* ------- SCROLL BAR -------*/
/* width */
.tabs::-webkit-scrollbar {
  height: 3px;
}

/* Track */
.tabs::-webkit-scrollbar-track {
  background: #8080806b;
}

/* Handle */
.tabs::-webkit-scrollbar-thumb {
  background: #f5f5f559;
}

/* Handle on hover */
.tabs::-webkit-scrollbar-thumb:hover {
  background: #f5f5f59e;
}

@media (max-width: 815px) {
  .settings-box {
    width: 100%;
  }
}
@media (max-height: 609px) {
  .settings-box {
    height: 100%;
  }
}
@media (max-width: 550px) {
  .settings-box {
    flex-direction: column;
  }
  .tabs {
    height: 50px;
    width: 100%;
    display: flex;
    overflow: hidden;
    overflow-x: auto;
    flex-shrink: 0;
  }
  .tab {
    flex-shrink: 0;
  }
  .panel {
    width: 100%;
  }
}
</style>

