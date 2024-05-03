<script setup lang="ts">
defineProps<{
  lifts: number
  status: boolean
}>()
</script>

<template>
  <div class="elevator">
    <img src="../assets/arrow.svg" alt="arrow" :hidden="status">
  </div>
</template>

<style scoped>
.elevator {
  background-color: #2da0c9;
}
</style>
