<template>
  <div class="pending-friends" >
    <div class="tab" @click="expanded = !expanded">
      <Tab :expanded="expanded" tabname="Pending requests" />
    </div>
    <transition name="list" appear>
      <div class="list" v-if="expanded">
        <PendingTemplate v-for="(friend, key) of friends"  :key="key" :uniqueID="friend.recipient.uniqueID" :status="friend.status" :username="friend.recipient.username" :tag="friend.recipient.tag"/>
      </div>
    </transition>
  </div>
</template>

<script>
import Tab from './Tab.vue'
import PendingTemplate from './PendingTemplate.vue'
export default {
  components: {
    Tab,
    PendingTemplate
  },
  data() {
    return {
      expanded: true
    } 
  },
  computed: {
    friends() {
      const allFriend = this.$store.getters.user.friends;
      const result = Object.keys(allFriend).map(function(key) {
        return allFriend[key];
      });
      return result.filter(friend => friend.status < 2);
    }
  }
}
</script>
<style scoped>
.list-enter-active, .list-leave-active {
  transition:  .3s;
}
.list-enter, .list-leave-to /* .fade-leave-active below version 2.1.8 */ {
  transform: translateY(-20px);
  opacity: 0;
}

.pending-friends{
  background-color: rgba(0, 0, 0, 0);
  margin: 5px;
  user-select: none;
  padding-bottom: 3px;
  border-radius: 5px;
  transition: 0.3s;
}
.tab{
  border-radius: 5px;
  transition: 0.3s;
}
.tab:hover{
  background-color: rgba(0, 0, 0, 0.123);
}
</style>


