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
  name: 'app',
}
</script>

<style>
</style>
