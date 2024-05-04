<script setup lang="ts">
import Elevator from "@/components/Elevator.vue";
import {reactive, ref} from "vue";

interface Store {
  status: boolean;
  stage: number;
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
});

const liftStage = ref<number>(1);

const status = ref<boolean>(true);

const currentStage = ref<number>(1);

const handleLiftCall = (n:number) => {
  if (n === liftStage.value) {
    return;
  }

  setTimeout(() => {
    currentStage.value = n;
    // const liftGrid: HTMLElement = document.getElementById('grid-lift')!;
    // const liftCurr: HTMLElement = document.getElementById('elevator')!;
    // liftCurr.style.cssText = '';
  }, 1000);
}
</script>

<template>
  <main>
    <div id="grid-lift">
      <Elevator :lifts="settings.listOfLifts" :status="status" :key="lift" :stage="liftStage" v-for="lift in settings.listNum"/>
    </div>
    <div class="liftStage">
      <div class="stage" v-for="n in settings.listNum" :key="n">
        {{n}}
        <button class="btn-lft" @click="() => handleLiftCall(n)">{{n}}</button>
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
