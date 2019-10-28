<template>
  <div id="app">
    <GmapMap
      :center="{lat: 10, lng:10}"
      :zoom="15"
      map-type-id="terrain"
      style="width: 100%; height: 100%"
    >
    </GmapMap>
  </div>
</template>

<script>
export default {
  name: 'app',
}
</script>

<style>
</style>
