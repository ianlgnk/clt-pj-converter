<script setup lang="ts">
import BoxConverter from "@/components/BoxConverter.vue";
import BoxInfo from "@/components/BoxInfo.vue";
</script>

<template>
  <BoxConverter />
  <br />
  <BoxInfo />
</template>

<style lang="scss">
* {
  margin: 0;
}

#app {
  position: relative;
  width: 100vw;
  height: 100vh;
}
</style>
