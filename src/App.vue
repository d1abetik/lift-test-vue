<script setup lang="ts">
import Elevator from "@/components/Elevator.vue";
import {reactive, ref, toRef} from "vue";

interface Store {
  status: boolean;
  stage: number;
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
  status: true,
  stage: 1,
  currentStage: 1,
});

const status = toRef(store, 'status');

const stage = toRef(store, 'stage');

const currentStage = toRef(store, 'currentStage');

const disabledBtn = ref<boolean>(false);

const handleOffBtn = async () => {
  for (let i = 0; i < 2; i += 1) {
    await new Promise((resolve) => setTimeout(resolve, 3000));
    disabledBtn.value = !disabledBtn.value;
  }
}

const handleLiftCall = (n:number) => {
  if (n === currentStage.value) {
    return;
  }

  stage.value = n;

  const step = Math.abs(n - currentStage.value);

  status.value = false;

  if (n > currentStage.value) {
    const inter = setInterval(() => currentStage.value += 1, 1000);
    setTimeout(() => clearInterval(inter), 1000 * step);
  } else {
    const inter = setInterval(() => currentStage.value -= 1,1000);
    setTimeout(() => clearInterval(inter), 1000 * step);
  }
  status.value = true;

  handleOffBtn();
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
        <button :class="`btn-lft ${disabledBtn ? 'disabled' : ''}`" @click="() => handleLiftCall(n)" :disabled="disabledBtn">{{n}}</button>
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
