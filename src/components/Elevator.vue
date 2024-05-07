<script setup lang="ts">
const props = defineProps<{
  lifts: number
  status: boolean,
  stage: number,
  currentStage: number
}>();

console.log(props.status);
</script>

<template>
  <div :id="String(props.stage)" :class="`elevator ${props.stage === props.currentStage ? 'active' : 'inactive'}`">
    <div :hidden="props.stage !== props.currentStage">
      <span>{{props.stage}}</span>
      <img src="../assets/arrowDwn.svg" alt="arrow" :class="`${props.stage > props.currentStage ? 'rev' : ''}`">
    </div>
  </div>
</template>

<style scoped>
.elevator {
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
}

.elevator span {
  position: absolute;
  color: white;
  font-size: 30px;
  z-index: 2;
  left: 40px;
  top: 70px;
}

.rev {
  transform: rotate(180deg);
}

.disabled {
  opacity: 0;
}

.abled {
  opacity: 1;
}

.elevator > div > img {
  height: 40px;
  width: 40px;
  position: absolute;
  z-index: 1;
  top: 70px;
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
