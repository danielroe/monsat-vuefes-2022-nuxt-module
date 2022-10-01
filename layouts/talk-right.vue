<script setup lang="ts">
import { computed } from 'vue'
import { handleBackground } from '@slidev/client/layoutHelper'

const props = defineProps({
  image: {
    type: String,
  },
  class: {
    type: String,
  },
})

const style = computed(() => handleBackground(props.image))
</script>

<template>
  <div class="grid grid-cols-4 grid-rows-3  w-full h-full">
    <div class="col-span-4"></div>
    <div class="slidev-layout default col-span-3 ml-12 rounded-xl bg-dark-300 text-right balloon" :class="props.class">
      <slot />
    </div>
    <div class="w-full h-full main-image" :style="style" />
    <div class="col-span-4"></div>
  </div>
</template>

<style scoped>
.main-image {
  background-size: contain !important;
}

.balloon:after {
  content: "";
  position: absolute;
  top: 40%;
  right: 244px;
  width: 0px;
  height: 0px;
  margin: auto;
  border-style: solid;
  border-color: rgb(45, 45, 45) transparent transparent transparent;
  border-width: 20px 20px 20px 0;
  transform: rotate(135deg);
}
</style>
