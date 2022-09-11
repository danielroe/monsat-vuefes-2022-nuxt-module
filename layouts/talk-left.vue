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
    <div class="w-full h-full main-image" :style="style" />
    <div class="slidev-layout default col-span-3 mr-12 rounded-xl bg-dark-300" :class="props.class">
      <slot />
    </div>
    <div class="col-span-4"></div>
  </div>
</template>

<style scoped>
.main-image {
  background-size: contain !important;
}
</style>
