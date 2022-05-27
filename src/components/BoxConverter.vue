<script setup lang="ts">
import { isString } from "@vue/shared";
import { ref, computed } from "vue";

const grossPay = ref("");
const benefits = ref("");
const grossPayInput = ref<HTMLInputElement | null>(null);
const benefitsInput = ref<HTMLInputElement | null>(null);
const grossPayInputIcon = ref<HTMLDivElement | null>(null);
const benefitsInputIcon = ref<HTMLDivElement | null>(null);
const isGrossPayEmpty = computed(() => grossPay.value.length === 0);
const isBenefitsEmpty = computed(() => benefits.value.length === 0);

function onClickCalcular() {
  verifyInputStatus(["grossPay", "benefits"]);
  if (isGrossPayEmpty.value || isBenefitsEmpty.value) return;
  console.log("exec calculo...");
}

function onInput(event: Event) {
  const input = event.target as HTMLInputElement;
  let varsToCheck = [] as Array<string>;

  if (input === grossPayInput.value) {
    grossPay.value = formatBrlMoney(input.value);
    varsToCheck.push("grossPay");
  } else {
    benefits.value = input.value;
    varsToCheck.push("benefits");
  }

  verifyInputStatus(varsToCheck);
}

function onFocus(event: Event) {
  const input = event.target as HTMLInputElement;
  if (input === grossPayInput.value) {
    grossPayInputIcon.value?.classList.add("focused");
    grossPayInput.value?.classList.add("focused");
  } else {
    benefitsInputIcon.value?.classList.add("focused");
    benefitsInput.value?.classList.add("focused");
  }
}

function onBlur(event: Event) {
  const input = event.target as HTMLInputElement;
  let varsToCheck = [] as Array<string>;

  if (input === grossPayInput.value) {
    grossPayInputIcon.value?.classList.remove("focused");
    grossPayInput.value?.classList.remove("focused");
    varsToCheck.push("grossPay");
  } else {
    benefitsInputIcon.value?.classList.remove("focused");
    benefitsInput.value?.classList.remove("focused");
    varsToCheck.push("benefits");
  }

  verifyInputStatus(varsToCheck);
}

function verifyInputStatus(varsToBeChecked: Array<string>) {
  if (varsToBeChecked.indexOf("grossPay") !== -1) {
    if (isGrossPayEmpty.value) {
      grossPayInputIcon.value?.classList.add("warning");
      grossPayInput.value?.classList.add("warning");
    } else {
      grossPayInputIcon.value?.classList.remove("warning");
      grossPayInput.value?.classList.remove("warning");
    }
  }

  if (varsToBeChecked.indexOf("benefits") !== -1) {
    if (isBenefitsEmpty.value) {
      benefitsInputIcon.value?.classList.add("warning");
      benefitsInput.value?.classList.add("warning");
    } else {
      benefitsInputIcon.value?.classList.remove("warning");
      benefitsInput.value?.classList.remove("warning");
    }
  }
}

function formatBrlMoney(text: string): string {
  if (text === null || text === undefined) return text;
  return text.replace(/\D/g, "");
}
</script>

<template>
  <div class="container">
    <div class="box">
      <div class="main">
        <div class="form-item">
          <h2>Salário Bruto</h2>
          <div class="input-container">
            <div ref="grossPayInputIcon">
              <i class="fa-solid fa-sack-dollar" />
            </div>
            <input
              ref="grossPayInput"
              v-model="grossPay"
              placeholder="R$ 0000,00"
              @input="onInput"
              @focus="onFocus"
              @blur="onBlur"
            />
          </div>
        </div>
        <div class="form-item">
          <h2>Benefícios</h2>
          <div class="input-container">
            <div ref="benefitsInputIcon">
              <i class="fa-solid fa-credit-card" />
            </div>
            <input
              ref="benefitsInput"
              :value="benefits"
              placeholder="R$ 0000,00"
              @input="onInput"
              @focus="onFocus"
              @blur="onBlur"
            />
          </div>
        </div>
        <div class="form-footer">
          <button @click="onClickCalcular">Calcular</button>
        </div>
      </div>
    </div>
    <div class="footer">
      Developed with
      <i class="fa-solid fa-heart" />
    </div>
  </div>
</template>

<style lang="scss" scoped>
.container {
  width: calc(100% - 30px);
  padding: 0px 30px;
  display: flex;
  flex-direction: column;
  align-items: center;

  .box {
    max-width: calc(300px - 22px);
    width: calc(100% - 22px);
    background: #242424;
    padding: 10px 10px;
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

      .form-item {
        display: flex;
        flex-direction: column;
        align-items: center;
        padding: 0px 15px;
        margin-bottom: 15px;

        h2 {
          font-size: 14px;
          font-weight: 600;
          margin-bottom: 10px;
        }

        .input-container {
          width: 100%;
          display: flex;
          justify-content: center;

          input {
            width: 100%;
            padding: 5px 5px 5px 0;
            font-size: 12px;
            font-weight: 400;
            border-radius: 0 5px 5px 0;
            background: #1a1a1a;
            border-top: solid 1px transparent;
            border-bottom: solid 1px transparent;
            border-right: solid 1px transparent;
            color: #fff;
            transition: all 0.2s ease-in-out;

            &:focus {
              border-top: solid 1px #42b883 !important;
              border-bottom: solid 1px #42b883 !important;
              border-right: solid 1px #42b883 !important;
            }

            &.warning {
              border-top: solid 1px #d11507;
              border-bottom: solid 1px #d11507;
              border-right: solid 1px #d11507;
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

          div {
            padding: 0 8px 0 10px;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 12px;
            font-weight: 400;
            border-radius: 5px 0 0 5px;
            background: #1a1a1a;
            border-top: solid 1px transparent;
            border-bottom: solid 1px transparent;
            border-left: solid 1px transparent;
            color: #fff;
            transition: all 0.2s ease-in-out;

            &.focused {
              border-top: solid 1px #42b883 !important;
              border-bottom: solid 1px #42b883 !important;
              border-left: solid 1px #42b883 !important;
            }

            &.warning {
              border-top: solid 1px #d11507;
              border-bottom: solid 1px #d11507;
              border-left: solid 1px #d11507;
            }

            i {
              font-size: 12px;
              color: #808080;
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

  .footer {
    margin-top: 10px;
    text-align: center;
    font-size: 12px;

    i {
      color: #d11507;
    }
  }
}
</style>
