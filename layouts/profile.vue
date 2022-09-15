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
  <div class="grid grid-cols-5 w-full h-full">
    <div class="slidev-layout default col-span-3" :class="props.class">
      <slot />
    </div>
    <div class="col-span-2 mt-40 mr-14 mb-10" :style="style" />
  </div>
</template>
