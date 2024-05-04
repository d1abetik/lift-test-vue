<script setup lang="ts">

import {watchEffect} from "vue";

const props = defineProps<{
  lifts: number
  status: boolean,
  stage: number
}>();

watchEffect(() => {
  console.log()
  const doc = document.getElementById('elevator');
  if (doc) {
    doc.setAttribute('style', `grid-row-start: ${props.stage}`);
  }
})
</script>

<template>
  <div id="elevator" :style="`grid-row-start: ${props.stage}`" :class="`${props.stage ? 'active' : 'inactive'}`">
    <img src="../assets/arrow.svg" alt="arrow" :hidden="props.status">
  </div>
</template>

<style scoped>
#elevator {
  padding: 10px;
  height: 100%;
}

.active {
  background-color: #2da0c9;
  border: 1px solid #c5c5c5;
}

.inactive {
  background-color: white;
  border: 1px solid #c5c5c5;
}
</style>
