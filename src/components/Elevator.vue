<script setup lang="ts">
import type { Store } from "@/App.vue";

const {store, stage, lifts} = defineProps<{
  store: Store;
  stage: number;
  lifts: number;
}>()

</script>

<template>
  <div
    :id="`st-${stage}`"
    :class="[
      'elevator',
      stage === store.currentStage ? 'active' : 'inactive',
      store.waitingStatus ? 'lightup' : '',
      store.isRunning && store.currentStage === stage ? (store.upDown === 'up' ? 'el-up' : 'el-down') : ''
    ]
  ">
    <div v-if="store.currentStage === stage && store.status">
      <span>{{store.currentStage !== store.goalStage ? (store.upDown === 'up' ? stage + 1 : stage - 1) : stage}}</span>
      <img src="../assets/arrowDwn.svg" alt="arrow" :class="`${store.upDown === 'up' ? 'rev' : ''}`">
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

.el-up {
  animation: slideIn 0.5s ease-in-out forwards;
}

.el-down {
  animation: slideOut 0.5s ease-in-out forwards;
}

@keyframes slideIn {
  from {
    transform: translateY(0%);
  } to {
    transform: translateY(-100%);
  }
}

@keyframes slideOut {
  from {
    transform: translateY(0%);
  } to {
    transform: translateY(100%);
  }
}

.elevator > div > span {
  position: absolute;
  color: white;
  font-size: 30px;
  background-color: red;
  padding: 10px;
  z-index: 2;
  left: 40px;
  top: 70px;
}

.lightup {
  animation-name: blink;
  animation-timing-function: linear;
  animation-duration: 1.5s;
  animation-iteration-count: infinite;
}

.rev {
  transform: rotate(180deg);
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
  z-index: 4;
}

.inactive {
  background-color: white;
  border: 1px solid #c5c5c5;
}

@keyframes blink {
  50% {
    opacity: 0;
  }
}
</style>
