<template>
  <PointersPanel />
  <MapElement :map-url="`/src/assets/base-map-video.mp4`"/>
</template>

<script setup>
  import PointersPanel from './components/PointersPanel.vue';
  import MapElement from './components/MapElement.vue';
</script>