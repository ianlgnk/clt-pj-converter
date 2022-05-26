<script setup lang="ts">
import { ref, computed } from "vue";

const grossPay = ref("");
const benefits = ref("");
const grossPayInput = ref<HTMLInputElement | null>(null);
const benefitsInput = ref<HTMLInputElement | null>(null);
const isGrossPayEmpty = computed(() => grossPay.value.length === 0);
const isBenefitsEmpty = computed(() => benefits.value.length === 0);

function onClickCalcular() {
  verifyInputStatus();
  if (isGrossPayEmpty.value || isBenefitsEmpty.value) return;
  console.log("exec calculo...");
}

function onInput(event: Event) {
  const input = event.target as HTMLInputElement;
  if (input === grossPayInput.value) grossPay.value = input.value;
  else benefits.value = input.value;
  verifyInputStatus();
}

function verifyInputStatus() {
  if (isGrossPayEmpty.value) grossPayInput.value?.classList.add("warning");
  else grossPayInput.value?.classList.remove("warning");

  if (isBenefitsEmpty.value) benefitsInput.value?.classList.add("warning");
  else benefitsInput.value?.classList.remove("warning");
}
</script>

<template>
  <div class="box">
    <div class="header"></div>
    <div class="main">
      <div class="form-item">
        <h2>Salário Bruto</h2>
        <div class="input-container">
          <i class="fa-solid fa-sack-dollar" />
          <input
            ref="grossPayInput"
            :value="grossPay"
            placeholder="R$ 0000,00"
            @input="onInput"
            @blur="verifyInputStatus"
          />
        </div>
      </div>
      <div class="form-item">
        <h2>Benefícios</h2>
        <div class="input-container">
          <i class="fa-solid fa-credit-card"></i>
          <input
            ref="benefitsInput"
            :value="benefits"
            placeholder="R$ 0000,00"
            @input="onInput"
            @blur="verifyInputStatus"
          />
        </div>
      </div>
      <div class="form-footer">
        <button @click="onClickCalcular">Calcular</button>
      </div>
    </div>
  </div>
  <div class="box-footer">
    Developed with
    <i class="fa-solid fa-heart" />
  </div>
</template>

<style lang="scss" scoped>
.box {
  max-width: 300px;
  background: #242424;
  padding: 5px 10px;
  border-radius: 10px;
  border: solid 1px #42b883;
  display: flex;
  flex-direction: column;

  .header {
    display: flex;
    justify-content: center;
    font-size: 20px;
    font-weight: 600;
    letter-spacing: 1.5px;
    margin-bottom: 20px;
  }

  .main {
    display: flex;
    flex-direction: column;
    margin-bottom: 10px;

    .form-item {
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 0px 15px;
      margin-bottom: 15px;

      h2 {
        font-size: 14px;
        font-weight: 600;
        margin-bottom: 5px;
      }

      .input-container {
        width: 100%;
        position: relative;

        input {
          width: calc(100px - 35px);
          padding: 5px 5px 5px 30px;
          font-size: 12px;
          font-weight: 400;
          border-radius: 5px;
          background: #1a1a1a;
          border: solid 1px transparent;
          color: #fff;
          transition: all 0.2s ease-in-out;

          &:focus {
            border: solid 1px #42b883;
          }

          &.warning {
            border: solid 1px #d11507;
          }

          // Chrome, Firefox, Opera, Safari 10.1+
          &::placeholder {
            color: #808080;
            opacity: 1;
          }
          // Internet Explorer 10-11
          &:-ms-input-placeholder {
            color: #808080;
          }
          // Microsoft Edge
          &::-ms-input-placeholder {
            color: #808080;
          }
        }

        i {
          font-size: 12px;
          position: absolute;
          color: #808080;

          &.fa-solid.fa-sack-dollar {
            top: 7px;
            left: 10px;
          }

          &.fa-solid.fa-credit-card {
            top: 8px;
            left: 10px;
          }
        }
      }
    }

    .form-footer {
      display: flex;
      align-items: center;
      justify-content: center;

      button {
        cursor: pointer;
        padding: 5px 20px;
        border-radius: 5px;
        background: #1a1a1a;
        color: #42b883;
        font-size: 14px;
        font-weight: 800;
        transition: all 0.2s ease-in-out;

        &:hover {
          border-radius: 10px;
          background: #42b883;
          color: #1a1a1a;
        }
      }
    }
  }
}

.box-footer {
  margin-top: 10px;
  text-align: center;
  font-size: 12px;

  i {
    color: #d11507;
  }
}
</style>
