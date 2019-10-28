<template>
  <div id="app">
    <GmapMap
      :center="{lat: initialPosition.lat, lng:initialPosition.lng}"
      :zoom="10"
      map-type-id="terrain"
      style="width: 100%; height: 100%"
    >
      <!-- check if icon link in makers payload then display -->
      <GmapMarker
        :key="index"
        v-for="(m, index) in markers"
        :position="m.position"
        :clickable="true"
        :draggable="false"
        @click="center=m.position"
        :icon="m.icon"
        :title="m.userName"
      />
    </GmapMap>
  </div>
</template>

<script>
import axios from "axios";
import * as Ably from "ably";
import Loading from "vue-loading-overlay";
import "vue-loading-overlay/dist/vue-loading.css";

var ably = new Ably.Realtime("");
var ably = new Ably.Realtime({
  key: "",
  clientId: `${Math.random() * 1000000}`
});
var channel = ably.channels.get("friendsroom");
export default {
  name: "app",
  mounted() {
    const name = prompt('To get started, input your name in the field below and locate your friends around based on your location, please turn on your location setting \n What is your name?')
    this.usersName = name
  },
  async created() {
    await this.fetchData();
    await channel.attach(err => {
      if (err) {
        return console.error("Error attaching to the channel");
      }
      console.log("We are now attached to the channel");
      channel.presence.enter(this.userlocation, function(err) {
        if (err) {
          return console.error("Error entering presence");
        }
        console.log("We are now successfully present");
      });
    });

    let membersData;
    channel.presence.subscribe(function(presenceMsg) {
      console.log(
        "Received a " + presenceMsg.action + " from " + presenceMsg.clientId
      );
      channel.presence.get(function(err, members) {
        membersData = members
        console.log(
          "There are now " + members.length + " clients present on this channel"
        );
      });
    });
    this.polling = setInterval(() => {
      this.markers = membersData.map((mem) => {
        if (JSON.stringify(this.userlocation) == JSON.stringify(mem.data)) {
          return {...mem.data, icon: 'http://maps.google.com/mapfiles/ms/icons/blue-dot.png'}
        } else {
          return {...mem.data, icon: 'http://maps.google.com/mapfiles/ms/icons/red-dot.png'}
        }
      })
      // membersData.map((mem) => {
      //   console.log(JSON.stringify(this.userlocation) == JSON.stringify(mem.data))
      // })
    }, 3000);
  },
}
</script>

<style>
</style>
