<script setup lang="ts">
import BoxConverter from "@/components/BoxConverter.vue";
import BoxInfo from "@/components/BoxInfo";1
import { ref } from "vue";

const infoIcon = ref("fa-solid fa-question");
const BoxInfoComponent = ref<InstanceType<typeof BoxInfo> | null>(null);

function onClickInfoBtn() {
  if (infoIcon.value === "fa-solid fa-question") {
    infoIcon.value = "fa-solid fa-xmark";
    BoxInfoComponent.value?.open();
  } else {
    infoIcon.value = "fa-solid fa-question";
    BoxInfoComponent.value?.close();
  }
}
</script>

<template>
  <BoxConverter />
  <button
    class="btn-info"
    @click="onClickInfoBtn"
  >
    <i :class="infoIcon" />
  </button>

  <BoxInfo
    ref="BoxInfoComponent"
  />
</template>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
  outline: 0;
  border: 0;
}

#app {
  position: relative;
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background: #1a1a1a;
  color: #FFF;
}

.btn-info {
  position: absolute;
  bottom: 10px;
  background: transparent;
  background: #e0ad15;
  color: #1a1a1a;
  cursor: pointer;
  width: 30px;
  height: 30px;
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 100px;
  transition: all 0.2s;
  z-index: 2;

  i {
    font-size: 15px;
  }

  &:hover {
    color: #FFF;
  }
}
</style>
