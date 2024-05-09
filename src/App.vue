<script setup lang="ts">
import Elevator from "@/components/Elevator.vue";
import {reactive, watch} from "vue";

export interface Store {
  status: boolean;
  currentStage: number;
  goalStage: number;
  callQueue: number[];
  isRunning: boolean;
  waitingStatus: boolean;
  upDown: string;
}

const reverseList = (n:number) => {
  const res = [];
  for (let i = n; i > 0; i -= 1) {
    res.push(i);
  }
  return res;
}

const settings = {
  listNum: reverseList(5),
  listOfLifts: 1
}

const store: Store = reactive({
  status: false,
  goalStage: 0,
  currentStage: 1,
  callQueue: [],
  isRunning: false,
  waitingStatus: false,
  upDown: '',
});

const saveToLocalStorage = (store: Store) => {
  localStorage.setItem('data', JSON.stringify(store));
};

watch(store, (newValue) => {
  saveToLocalStorage(newValue);
}, { deep: true });

const handleOffBtn = async () => {
  store.waitingStatus = true;
  await new Promise((resolve) => setTimeout(resolve, 3000));
  store.goalStage = 0;
  store.isRunning = false;
  store.waitingStatus = false;
}

const runLift = async (n:number) => {
  const step = Math.abs(n - store.currentStage);

  for (let i = 0; i < step; i += 1) {
    if (n > store.currentStage) {
      store.upDown = 'up';
      await new Promise(resolve => setTimeout(() => {
        store.currentStage += 1;
        resolve('done');
      }, 1000));
    } else {
      store.upDown = 'down';
      await new Promise(resolve => setTimeout(() => {
        store.currentStage -= 1;
        resolve('done');
      }, 1000));
    }
  }
  await handleOffBtn();
}

const handleLiftCall = async (n:number) => {
  if (n === store.currentStage) {
    return;
  }

  if (store.callQueue.includes(n)) {
    return;
  }

  store.callQueue.push(n);

  if (store.isRunning) {
    return;
  }

  const recursiveLiftStack = async () => {
    store.isRunning = true;
    const nextCall = store.callQueue.shift();
    if (nextCall) {
      store.goalStage = nextCall;
      await runLift(nextCall);
    }

    if (store.callQueue.length !== 0) {
      await recursiveLiftStack();
    }
  }
  store.status = true;
  await recursiveLiftStack();
  store.status = false;
  console.log('end');
}

if (localStorage.getItem('data')) {
  const savedDataString = localStorage.getItem('data');
  if (savedDataString) {
    const savedData = JSON.parse(savedDataString);
    Object.assign(store, savedData);

    if (store.goalStage || store.callQueue.length > 0) {
      store.isRunning = false;
      handleLiftCall(store.goalStage ?? store.callQueue[0]);
    }
  }
}
</script>

<template>
  <main>
    <div id="grid-lift">
      <Elevator v-for="lift in settings.listNum" :lifts="settings.listOfLifts" :store="store" :key="lift" :stage="lift"/>
    </div>
    <div class="liftStage">
      <div class="stage" v-for="n in settings.listNum" :key="n">
        <button type="button" :class="`${store.callQueue.includes(n) || n === store.goalStage && store.isRunning ? 'waiting' : 'btn-lft'}`" @click="() => handleLiftCall(n)">{{n}}</button>
      </div>
    </div>
  </main>
</template>

<style scoped>
.liftStage {
  height: 100%;
  display: grid;
}

.stage {
  width: 100%;
  height: 100%;
  align-content: center;
}

#grid-lift {
  display: grid;
  grid-template-rows: repeat(5, auto);
  grid-auto-rows: 1fr;
}

.btn-lft {
  width: 40px;
  height: 40px;
  background-color: #2da0c9;
  cursor: pointer;
  outline: none;
  margin-left: 60px;
}

.waiting {
  width: 40px;
  height: 40px;
  background-color: purple;
  cursor: pointer;
  outline: none;
  margin-left: 60px;
}

.active-btn {
  width: 40px;
  height: 40px;
  background-color: blue;
  cursor: pointer;
  outline: none;
  margin-left: 60px;
}

.disabled {
  background-color: grey;
  cursor: not-allowed;
}

.btn-lft:hover {
  background-color: #a8d3e7;
}

.stage {
  width: 100%;
  border: 1px solid #d5d5d5;
}

main {
  display: grid;
  color: #262626;
  font-size: 20px;
  width: 100%;
  height: 100vh;
  grid-template-columns: 1fr 6fr;
  grid-auto-rows: minmax(100px, auto);
}
</style>
