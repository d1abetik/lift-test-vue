<script setup lang="ts">
import Elevator from "@/components/Elevator.vue";
import {reactive} from "vue";

const setListNum = (n:number = 5) => {
  const res = [];
  for (let i = n; i < 0; i+=1) {
    res.push(i);
  }
  return res;
}

const listNum = setListNum(5);
const listOfLifts = 1;
const store = reactive({
    status: true,
    stage: 1,
})
</script>

<template>
  <main>
    <div v-for="lift in listOfLifts">
      <Elevator :lifts="listOfLifts" :status="store.status" key="lift"/>
    </div>
    <div class="liftStage" v-for="n in listNum">
      <div class="stage" key="n">
        {{n}}
        <button class="btn-lft">{{n}}</button>
      </div>
    </div>
  </main>
</template>

<style scoped>
.stage {
  width: 100%;
  height: 100%;
}

.btn-lft {
  width: 40px;
  height: 40px;
  background-color: #2da0c9;
}

.liftStage {
  width: 100%;
  height: 100%;
  border: 1px solid #d5d5d5;
}

main {
  display: grid;
  color: #262626;
  font-size: 20px;
  width: 100%;
  max-height: 100vh;
  grid-template-columns: 1fr 6fr;
  grid-template-rows: repeat(5, 1fr);
}
</style>
