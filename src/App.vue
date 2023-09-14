<template>
  <PointersPanel />
  <MapElement :map-url="`/src/assets/images/base-map.jpg`"/>
</template>

<script setup>
  import PointersPanel from './components/PointersPanel.vue';
  import MapElement from './components/MapElement.vue';
</script>