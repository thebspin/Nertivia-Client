<template>
  <div :class="{message: true,  ownMessage: user.uniqueID === $props.uniqueID, ownMessageLeft: user.uniqueID === $props.uniqueID && (apperance && apperance.own_message_right === true)} ">
    <profile-picture class="avatar" :admin="$props.admin" :url="userAvatar" size="50px" :hover="true" @click.native="openUserInformation"/>
    <div class="triangle">
      <div class="triangle-inner"></div>
    </div>
    <div class="content">
      <div class="user-info">
        <div class="username" @click="openUserInformation">{{this.$props.username}}</div>
        <div class="date">{{getDate}}</div>
      </div>
      <div class="content-message" v-html="formatMessage"></div>

      <div class="file-content" v-if="getFile">
        <div class="icon">
          <i class="material-icons">insert_drive_file</i>
        </div>
        <div class="information">
          <div class="info">{{getFile.fileName}}</div>
          <a :href="getFile.url" target="_blank">
            <div class="download-button">Download</div>
          </a>
        </div>
      </div>

      <div class="image-content" v-if="getImage">
        <img :src="getImage" @click="imageClicked">
      </div>
    </div>
    <div class="sending-status" v-html="statusMessage"></div>
  </div>
</template>


<script>
import ProfilePicture from "@/components/ProfilePictureTemplate.vue";
import messageFormatter from "@/utils/messageFormatter.js";
import config from "@/config.js";
import friendlyDate from "@/utils/date";
import path from "path";

import { mapState } from "vuex";

export default {
  components: {
    ProfilePicture
  },
  props: [
    "message",
    "status",
    "username",
    "avatar",
    "date",
    "uniqueID",
    "files",
    "admin"
  ],
  methods: {
    openUserInformation() {
      this.$store.dispatch('setUserInformationPopout', this.uniqueID)
    },
    imageClicked(event) {
      this.$store.dispatch("setImagePreviewURL", event.target.src);
    }
  },
  computed: {
    ...mapState('settingsModule', ['apperance']),
    getImage() {
      if (!this.$props.files || this.$props.files.length === 0)
        return undefined;
      const file = this.$props.files[0];
      if (!file.fileID) return undefined;
      const filetypes = /jpeg|jpg|gif|png/;
      const extname = filetypes.test(path.extname(file.fileName).toLowerCase());
      if (!extname) return undefined;
      return config.domain + "/files/" + file.fileID;
    },
    getFile() {
      if (!this.$props.files || this.$props.files.length === 0)
        return undefined;
      let file = this.$props.files[0];
      if (!file.fileID) return undefined;
      const filetypes = /jpeg|jpg|gif|png/;
      const extname = filetypes.test(path.extname(file.fileName).toLowerCase());
      if (extname) return undefined;
      file.url = config.domain + "/files/" + file.fileID;
      return file;
    },
    formatMessage() {
      return messageFormatter(this.$props.message);
    },
    getDate() {
      return friendlyDate(this.$props.date);
    },
    userAvatar() {
      return config.domain + "/avatars/" + this.$props.avatar;
    },
    statusMessage() {
      let status = this.$props.status;

      if (status == 0) {
        return `<i class="material-icons">hourglass_full</i>`;
      } else if (status == 1) {
        return `<i class="material-icons">done</i>`;
      } else if (status == 2) {
        return `<i class="material-icons">close</i> Failed`;
      } else {
        return "";
      }
    },
    user() {
      return this.$store.getters.user;
    }
  }
};
</script>


<style scoped>

.ownMessageLeft  {
  flex-direction: row-reverse;
}

.ownMessageLeft .triangle-inner {
  transition: 0.5s;
  border-left: 7px solid rgba(184, 184, 184, 0.219);
  border-right: none !important;
}
.ownMessageLeft .avatar { 
    margin-right: 0px;
    margin-left: 5px;
}
.ownMessageLeft .sending-status{
  margin-left: auto !important;
  margin-right: 4px !important;
}



.message {
  margin: 10px;
  margin-top: 10px;
  margin-bottom: 10px;
  display: flex;

  animation: showMessage 0.3s ease-in-out;
}

.ownMessage .triangle-inner {
  transition: 0.5s;
  border-right: 7px solid rgba(184, 184, 184, 0.219);
}
.ownMessage .content {
  background: rgba(184, 184, 184, 0.219);
}
.ownMessage .date {
  color: rgb(209, 209, 209);
}

.file-content {
  display: flex;
  background: rgba(0, 0, 0, 0.089);
  padding: 10px;
  margin-top: 5px;
}
.file-content .material-icons {
  font-size: 40px;
}
.file-content .download-button {
  font-size: 14px;
  background: rgba(0, 0, 0, 0.158);
  border-radius: 3px;
  padding: 3px;
  text-align: center;
  display: inline-block;
  margin-top: 3px;
  transition: 0.3s;
  user-select: none;
  cursor: default;
  color: white;
}
.file-content .download-button:hover {
  background: rgba(0, 0, 0, 0.329);
}
.file-content .info {
  word-wrap: break-word;
  word-break: break-word;
  white-space: pre-wrap;
  font-size: 14px;
  overflow: hidden;
  max-width: 100%;
  color: white;
  overflow-wrap: anywhere;
  margin-top: 3px;
}

@keyframes showMessage {
  from {
    transform: translate(0px, 9px);
    opacity: 0;
  }
}

.avatar {
  margin: auto;
  margin-left: 0;
  margin-right: 5px;
  margin-bottom: 0;
}

.triangle {
  display: flex;
  justify-content: bottom;
  flex-direction: column;
  margin: auto;
  margin-left: 0;
  margin-right: 0px;
  margin-bottom: 8.7px;
}
.triangle-inner {
  width: 0;
  height: 0;

  border-top: 1px solid transparent;
  border-bottom: 7px solid transparent;
  border-right: 7px solid rgba(0, 0, 0, 0.301);
}

.content {
  background: rgba(0, 0, 0, 0.301);
  padding: 10px;
  display: flex;
  justify-content: center;
  flex-direction: column;
  border-radius: 10px;
  color: rgb(231, 231, 231);
  margin: auto;
  margin-left: 0;
  margin-right: 0;
  transition: 1s;
  overflow: hidden;
}

.image-content {
  margin-top: 10px;
  padding: 5px;
  border-radius: 5px;
  background: rgba(0, 0, 0, 0.493);
  display: -ms-flexbox;
  display: flex;
  flex-direction: column;
}
.image-content img {
  width: 170px;
  height: auto;
  transition: 0.2s;
}
.image-content:hover img {
  filter: brightness(70%);
}
.user-info {
  display: flex;
}
.username {
  color: rgb(219, 219, 219);
  font-size: 14px;
  margin: auto;
  margin-left: 0;
  margin-right: 0;
  transition: 0.1s;
  cursor: default;
}
.username:hover{
  color: rgb(199, 199, 199);
  text-decoration: underline;
}
.date {
  color: rgb(161, 161, 161);
  font-size: 10px;
  margin: auto;
  margin-left: 5px;
}
.content-message {
  word-wrap: break-word;
  word-break: break-word;
  white-space: pre-wrap;
  font-size: 14px;
  overflow: hidden;
  max-width: 100%;
  color: white;
  overflow-wrap: anywhere;
  margin-top: 3px;
}

.message .sending-status {
  display: flex;
  justify-content: flex-end;
  padding-bottom: 5px;
  font-size: 15px;
  color: white;
  margin: auto;
  margin-left: 4px;
  margin-bottom: 0;
  user-select: none;
  transition: 0.5;
  align-content: center;
}
</style>

<style>
.message .sending-status .material-icons {
  font-size: 15px;
  color: rgb(306, 306, 306);
  display: block;
}
.codeblock {
  background-color: rgba(0, 0, 0, 0.397);
  padding: 5px;
  border-radius: 5px;
}
img.emoji {
  height: 1.7em;
  width: auto;
  margin: 0 0.05em 0 0.1em;
  vertical-align: -0.1em;
}
</style>
