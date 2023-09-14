<template>
 <svg
    ref="mapAsset"
    class="absolute min-h-full min-w-full transition-opacity"
    :class="[{ 'opacity-0': isAnalyzingMap, 'w-full h-auto': fullWidthMode, 'w-auto h-full': fullHeightMode }]"
    width="100%"
    height="100%"
    viewBox="0 0 1920 1080"
    version="1.1"
  >
    <template v-if="mapUrl">
      <image
        :href="mapUrl"
        class="w-full h-full"
        x="0"
        y="0"
        height="1920"
        width="1080"
      />
    </template>

    <rect
      ref="rectAsset"
      width="100%"
      height="100%"
      x="0"
      y="0"
      fill="transparent"
    />
    
    <a>
      <image x="1000" y="500" :xlink:href="`/src/assets/images/SVG/Totem.svg`" />
    </a>

    <a>
      <image x="830" y="130" :xlink:href="`/src/assets/images/SVG/Hut.svg`" />
    </a>
  </svg>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount, nextTick } from "vue";

defineProps({
  mapUrl: {
    type: String,
    default: null,
  }
})

const mapAsset = ref(null);
const rectAsset = ref(null);
const fullWidthMode = ref(true);
const fullHeightMode = ref(false);
const isAnalyzingMap = ref(true);
let resizeTimer;

onMounted(() => {
  window.addEventListener("resize", checkForOverflow);
  mapAsset.value.addEventListener("load", checkForOverflow);
});

onBeforeUnmount(() => {
  window.removeEventListener("resize", checkForOverflow);
});

const checkForOverflow = () => {
  isAnalyzingMap.value = true;
  clearTimeout(resizeTimer);
  resizeTimer = setTimeout(() => {
    isAnalyzingMap.value = false;

    const rect = rectAsset.value.getBoundingClientRect();
    const imageWidth = rect.width;
    const imageHeight = rect.height;
    const containerWidth = window.innerWidth;
    const containerHeight = window.innerHeight;

    if (fullWidthMode.value && imageHeight < containerHeight) {
      fullWidthMode.value = false;
      fullHeightMode.value = true;
      centerHorizontalScroll();
      return;
    }

    if (fullHeightMode.value) {
      centerHorizontalScroll();
      
      if (imageWidth < containerWidth) {
        fullHeightMode.value = false;
        fullWidthMode.value = true;
        return;
      }
    }
  }, 200);
};

const centerHorizontalScroll = async () => {
  await nextTick();
  const scrollElement = mapAsset.value.parentElement;
  scrollElement.scrollLeft = (scrollElement.scrollWidth - scrollElement.clientWidth) / 2;
}
</script>