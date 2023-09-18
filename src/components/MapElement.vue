<template>
 <svg
    ref="mapAsset"
    class="map-container absolute min-h-full min-w-full transition-opacity"
    :class="[{ 'opacity-0': isAnalyzingMap, 'w-full h-auto': fullWidthMode, 'w-auto h-full': fullHeightMode }]"
    width="100%"
    height="100%"
    viewBox="0 0 1920 1080"
    version="1.1"
  >
    <template v-if="mapUrl">
      <foreignObject
        v-if="mapIsVideo"
        x="0"
        y="0"
        height="1080px"
        width="1920px"
      >
        <video
          class="w-full h-full"
          autoplay
          loop
          muted
        >
          <source
            :src="mapUrl"
            type="video/mp4"
          >
        </video>
      </foreignObject>

      <image
        v-else
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
      class="dropzone"
      width="100%"
      height="100%"
      x="0"
      y="0"
      fill="transparent"
    />

    <a 
      v-for="pointer in pointers"
      :key="pointer.key"
      class="dropzone"
    >
      <image
        class="draggable"
        :data-key="pointer.key"
        :x="pointer.x"
        :y="pointer.y"
        :xlink:href="pointer.image"
      />
    </a>
  </svg>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount, nextTick, computed } from "vue";
import { Droppable, Sensors } from '@shopify/draggable';

const props = defineProps({
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

const mapIsVideo = computed(() => {
  return ['mp4', 'mov'].includes(props.mapUrl?.split('.').pop() ?? null);
})

const pointers = ref([
  {
    key: 'dot-active',
    x: 1000,
    y: 500,
    image: '/src/assets/images/SVG/Dot-Active.svg',
  },
  {
    key: 'dot-check',
    x: 830,
    y: 130,
    image: '/src/assets/images/SVG/Dot-Check.svg',
  },
  {
    key: 'dot-disabled',
    x: 400,
    y: 450,
    image: '/src/assets/images/SVG/Dot-Disabled.svg',
  }
])
let resizeTimer;

onMounted(() => {
  window.addEventListener("resize", checkForOverflow);
  mapAsset.value.addEventListener("load", checkForOverflow);

  const draggable = new Droppable(document.querySelectorAll('.map-container'), {
    draggable: '.draggable',
    dropzone: '.dropzone',
    sensors: [ Sensors.DragSensor ],
  });

  draggable.on('drag:start', (event) => {
    console.log(event);
  });

  draggable.on('drag:move', (event) => {
    console.log(event);
  });

  draggable.on('drag:stop', (event) => {
    console.log(event);
  });

  draggable.on('droppable:start', (event) => {
    console.log(event);
    console.log(event.dropzone.getBoundingClientRect());
  });

  draggable.on('droppable:return', (event) => {
    console.log(event);
    console.log(event.dropzone.getBoundingClientRect());
  });

  draggable.on('droppable:dropped', (event) => {
    console.log(event);
    console.log(event.dropzone.getBoundingClientRect());
  });
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
