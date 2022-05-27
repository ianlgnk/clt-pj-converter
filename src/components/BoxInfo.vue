<script setup lang="ts">
import { ref, defineExpose } from "vue";

const isVisible = ref(false);
const parentDiv = ref<HTMLElement | null>(null);

function open() {
  isVisible.value = true;
  parentDiv.value?.classList.add("visible");
}

function close() {
  isVisible.value = false;
  parentDiv.value?.classList.remove("visible");
}

defineExpose({
  open,
  close,
});
</script>

<template>
  <div ref="parentDiv">Aqui vai todas as informações sobre o conversor...</div>
</template>

<style lang="scss" scoped>
div {
  position: absolute;
  top: 0;
  padding: 10px 10px 50px 10px;
  width: calc(100vw - 20px);
  height: calc(100vh - 60px);
  transform: translateY(calc(-100vh - 60px));
  background: rgba(0, 0, 0, 0.8);
  transition: all 0.5s ease-in-out;
  z-index: 1;

  &.visible {
    transform: translateY(0);
  }
}
</style>
