<script setup lang="ts">
import Elevator from "@/components/Elevator.vue";
import {reactive, ref, toRef} from "vue";

interface Store {
  status: boolean;
  currentStage: number;
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
  currentStage: 1,
});

const status = toRef(store, 'status');

const currentStage = toRef(store, 'currentStage');

const callQueue = ref<number[]>([]);

const isRunning = ref(false);

const handleOffBtn = async () => {
  console.log('start timer dead 3s')
  await new Promise((resolve) => setTimeout(resolve, 3000));
  console.log('chill timer dead');
  isRunning.value = false;
}

const runLift = async (n:number) => {
  const step = Math.abs(n - currentStage.value);

  if (n > currentStage.value) {
    const inter = setInterval(() => currentStage.value += 1, 1000);
    await new Promise((resolve) => setTimeout(() => {
      clearInterval(inter)
      resolve('good');
    }, 1000 * step));
  } else {
    const inter = setInterval(() => currentStage.value -= 1,1000);
    await new Promise((resolve) => setTimeout(() => {
      clearInterval(inter)
      resolve('good');
    }, 1000 * step));
  }
  await handleOffBtn();
}

const handleLiftCall = async (n:number): Promise<void> => {
  if (n === currentStage.value) {
    return;
  }

  callQueue.value.push(n);

  if (isRunning.value) {
    return;
  }

  isRunning.value = true;

  console.log(callQueue.value);

  const recursiveLiftStack = async () => {
    const nextCall = callQueue.value.shift();
    if (nextCall) {
      console.log('start runLift');
      await runLift(nextCall);
    }

    if (callQueue.value.length !== 0) {
      await recursiveLiftStack();
    }
  }
  status.value = true;
  await recursiveLiftStack();

  status.value = false;
  console.log('end');
}
</script>

<template>
  <main>
    <div id="grid-lift">
      <div v-for="lift in settings.listNum">
        <Elevator :lifts="settings.listOfLifts" :status="status" :currentStage="currentStage" :key="lift" :stage="lift"/>
      </div>
    </div>
    <div class="liftStage">
      <div class="stage" v-for="n in settings.listNum" :key="n">
        <button :class="`btn-lft`" @click="() => handleLiftCall(n)">{{n}}</button>
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
