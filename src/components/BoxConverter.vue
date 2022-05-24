<script setup lang="ts"></script>

<template>
  <div class="box">
    <div class="main">
      <input />
    </div>
    <div class="footer"></div>
  </div>
  <span>
    Developed with
    <i class="fa-solid fa-heart" />
  </span>
</template>

<style lang="scss" scoped>
.box {
  width: 90%;
  background: #242424;
  padding: 10px;
  border-radius: 10px;
  border: solid 1px #42b883;
  margin-bottom: 10px;
  display: flex;
  flex-direction: column;

  .footer {
    display: flex;
    justify-content: center;
    width: 100%;

    button {
      display: flex;
      align-items: center;
      padding: 0 10px;
      height: 25px;
      border-radius: 5px;
      cursor: pointer;

      i {
        margin-right: 10px;
      }
    }
  }
}

span {
  font-size: 12px;

  i {
    color: #d11507;
  }
}
</style>
