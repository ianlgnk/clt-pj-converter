<script setup lang="ts">
import { ref, computed, defineExpose } from "vue";
import { IboxConverterInfo } from "./BoxInfo.vue";

const grossPay = ref("");
const benefits = ref("");
const dependents = ref("");
const pjPay = ref("");
const thirtheenth = ref(0);
const vacation = ref(0);
const fgts = ref(0);
const inss = ref(0);
const ir = ref(0);
const netSalary = ref(0);
const isBoxLoading = ref(false);
const isCalculated = ref(false);
const grossPayInput = ref<HTMLInputElement | null>(null);
const benefitsInput = ref<HTMLInputElement | null>(null);
const dependentsInput = ref<HTMLInputElement | null>(null);
const grossPayInputIcon = ref<HTMLDivElement | null>(null);
const benefitsInputIcon = ref<HTMLDivElement | null>(null);
const dependentsInputIcon = ref<HTMLDivElement | null>(null);
const btnCalcular = ref<HTMLButtonElement | null>(null);
const divContentForm = ref<HTMLDivElement | null>(null);
const divContentFormResult = ref<HTMLDivElement | null>(null);
const divContentStructure = ref<HTMLDivElement | null>(null);
const divContainer = ref<HTMLDivElement | null>(null);
const btnReset = ref<HTMLButtonElement | null>(null);
const isGrossPayEmpty = computed(() => grossPay.value.length === 0);
const isBenefitsEmpty = computed(() => benefits.value.length === 0);
const isDependentsEmpty = computed(() => dependents.value.length === 0);

function onClickCalcular() {
  verifyInputStatus();
  if (isGrossPayEmpty.value) return;

  const floatGrossPay = parseFloat(returnOnlyNumbers(grossPay.value)) / 100;
  let floatBenefits: number;
  if (isBenefitsEmpty.value) floatBenefits = 0;
  else floatBenefits = parseFloat(returnOnlyNumbers(benefits.value)) / 100;
  let intDependents: number;
  if (isDependentsEmpty.value) intDependents = 0;
  else intDependents = parseInt(dependents.value);

  const result = convertSalary(floatGrossPay, floatBenefits, intDependents);
  pjPay.value = formatBrlMoney(result);
  transitionAnimationBoxConverted();
}

function onClickIconReset() {
  btnReset.value?.classList.add("active");
  transitionAnimationBox();
}

function onInput(event: Event) {
  const input = event.target as HTMLInputElement;

  if (input === grossPayInput.value) {
    grossPay.value = formatBrlMoney(returnOnlyNumbers(input.value));
    verifyInputStatus();
  } else if (input === benefitsInput.value)
    benefits.value = formatBrlMoney(returnOnlyNumbers(input.value));
  else dependents.value = returnOnlyNumbers(input.value);
}

function onFocus(event: Event) {
  const input = event.target as HTMLInputElement;
  if (input === grossPayInput.value) {
    grossPayInputIcon.value?.classList.add("focused");
    grossPayInput.value?.classList.add("focused");
  } else if (input === benefitsInput.value) {
    benefitsInputIcon.value?.classList.add("focused");
    benefitsInput.value?.classList.add("focused");
  } else {
    dependentsInputIcon.value?.classList.add("focused");
    dependentsInput.value?.classList.add("focused");
  }
}

function onBlur(event: Event) {
  const input = event.target as HTMLInputElement;

  if (input === grossPayInput.value) {
    grossPayInputIcon.value?.classList.remove("focused");
    grossPayInput.value?.classList.remove("focused");
    verifyInputStatus();
  } else if (input === benefitsInput.value) {
    benefitsInputIcon.value?.classList.remove("focused");
    benefitsInput.value?.classList.remove("focused");
  } else {
    dependentsInputIcon.value?.classList.remove("focused");
    dependentsInput.value?.classList.remove("focused");
  }

  if (grossPay.value === "R$ " || grossPay.value === "R$ 0,00")
    grossPay.value = "";
  if (benefits.value === "R$ " || benefits.value === "R$ 0,00")
    benefits.value = "";
}

function verifyInputStatus() {
  if (isGrossPayEmpty.value) {
    grossPayInputIcon.value?.classList.add("warning");
    grossPayInput.value?.classList.add("warning");
  } else {
    grossPayInputIcon.value?.classList.remove("warning");
    grossPayInput.value?.classList.remove("warning");
  }
}

function returnOnlyNumbers(text: string): string {
  return text.replace(/\D/g, "");
}

function formatBrlMoney(text: string | number): string {
  let result;
  if (typeof text === "string") {
    result = new Intl.NumberFormat("pt-BR", {
      minimumFractionDigits: 2,
    }).format(parseFloat(text) / 100);
    if (typeof result === "string" && result !== "NaN") return `R$ ${result}`;
    else return "R$ ";
  } else {
    result = text.toLocaleString("pt-br", {
      style: "currency",
      currency: "BRL",
    });

    return result;
  }
}

function convertSalary(
  grossPay: number,
  benefits: number,
  dependents: number
): number {
  thirtheenth.value = grossPay / 12;
  vacation.value = grossPay / 3 / 12;
  fgts.value = grossPay * 0.08;
  inss.value = calcINSS(grossPay);
  ir.value = calcIR(grossPay, inss.value, dependents);
  netSalary.value = grossPay - (inss.value + ir.value);
  return (
    netSalary.value +
    inss.value +
    fgts.value +
    vacation.value +
    thirtheenth.value +
    benefits
  );
}

function calcINSS(grossPay: number): number {
  if (grossPay >= 7087.23) return 642.34;

  let result = 0;

  result += 1212.0 * 0.075;
  if (grossPay >= 1212.01) {
    if (grossPay >= 2427.36) result += (2427.35 - 1212.1) * 0.09;
    else result += (grossPay - 1212.1) * 0.09;
  }
  if (grossPay >= 2427.36) {
    if (grossPay >= 3641.04) result += (3641.03 - 2427.36) * 0.12;
    else result += (grossPay - 2427.36) * 0.12;
  }
  if (grossPay >= 3641.04) result += (grossPay - 3641.04) * 0.14;

  return result;
}

function calcIR(grossPay: number, INSS: number, dependents: number): number {
  const dependentsDiscount = 189.59 * dependents;
  const baseSalary = grossPay - (INSS + dependentsDiscount);

  if (baseSalary >= 1903.99 && baseSalary <= 2826.65)
    return baseSalary * 0.075 - 142.8;
  if (baseSalary >= 2826.66 && baseSalary <= 3751.05)
    return baseSalary * 0.15 - 354.8;
  if (baseSalary >= 3751.06 && baseSalary <= 4664.68)
    return baseSalary * 0.225 - 636.13;
  if (baseSalary >= 4664.69) return baseSalary * 0.275 - 869.36;

  return 0;
}

function transitionAnimationBoxConverted() {
  btnCalcular.value?.classList.add("btnLoading");
  isBoxLoading.value = true;
  btnCalcular.value?.classList.add("loading");
  setTimeout(() => {
    divContentForm.value?.classList.add("fade-out");
  }, 300);
  setTimeout(() => {
    divContentStructure.value?.classList.add("calculated");
  }, 1000);
  setTimeout(() => {
    btnCalcular.value?.classList.remove("loading");
    isCalculated.value = true;
    isBoxLoading.value = false;
  }, 1300);
}

function transitionAnimationBox() {
  isBoxLoading.value = true;
  setTimeout(() => {
    divContentFormResult.value?.classList.add("fade-out");
    divContentStructure.value?.classList.remove("calculated");
  }, 300);
  setTimeout(() => {
    isCalculated.value = false;
    isBoxLoading.value = false;
    pjPay.value = "";
  }, 1000);
  grossPay.value = "";
  benefits.value = "";
  dependents.value = "";
  vacation.value = 0;
  fgts.value = 0;
  inss.value = 0;
  ir.value = 0;
  netSalary.value = 0;
  thirtheenth.value = 0;
}

function switchOpacityComponent(type: boolean) {
  if (type) divContainer.value?.classList.add("hidden");
  else divContainer.value?.classList.remove("hidden");
}

function returnConversorData(): IboxConverterInfo {
  const dependentsDiscount = 189.59 * Number(dependents.value);
  const baseIRSalary =
    Number(returnOnlyNumbers(grossPay.value)) / 100 -
    (inss.value + dependentsDiscount);

  return {
    grossPay: grossPay.value,
    netSalary: netSalary.value === 0 ? "" : formatBrlMoney(netSalary.value),
    pjSalary: pjPay.value,
    benefits: benefits.value ? benefits.value : formatBrlMoney(0),
    dependents: dependents.value ? dependents.value : "0",
    inss: inss.value === 0 ? "" : formatBrlMoney(inss.value),
    ir: ir.value === 0 ? "" : formatBrlMoney(ir.value),
    fgts: fgts.value === 0 ? "" : formatBrlMoney(fgts.value),
    thirtheenth:
      thirtheenth.value === 0 ? "" : formatBrlMoney(thirtheenth.value),
    vacation: vacation.value === 0 ? "" : formatBrlMoney(vacation.value),
    inssPercent: findPercent(
      "INSS",
      Number(returnOnlyNumbers(grossPay.value)) / 100
    ),
    irPercent: findPercent("IR", baseIRSalary),
  };
}

function findPercent(type: string, baseSalary: number): string {
  if (baseSalary === 0) return "";
  if (type === "INSS") {
    if (baseSalary <= 1212.0) return "(7,5%)";
    else if (baseSalary >= 1212.01 && baseSalary <= 2427.35) return "(9%)";
    else if (baseSalary >= 2427.36 && baseSalary <= 3641.03) return "(12%)";
    else if (baseSalary >= 3641.04 && baseSalary <= 7087.22) return "(14%)";
    else return "(TETO)";
  } else {
    if (baseSalary <= 1903.98) return "(ISENTO)";
    else if (baseSalary >= 1903.99 && baseSalary <= 2826.65) return "(7.5%)";
    else if (baseSalary >= 2826.66 && baseSalary <= 3751.05) return "(15%)";
    else if (baseSalary >= 3751.06 && baseSalary <= 4664.68) return "(22,5%)";
    else return "(27,5%)";
  }
}

defineExpose({
  switchOpacityComponent,
  returnConversorData,
});
</script>

<template>
  <div ref="divContainer" class="container">
    <div class="header">
      <div>
        <i class="fas fa-dollar-sign"></i>
      </div>
    </div>
    <div ref="divContentStructure" class="box">
      <div v-if="!isCalculated" ref="divContentForm" class="form">
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
              v-model="benefits"
              placeholder="R$ 0000,00"
              @input="onInput"
              @focus="onFocus"
              @blur="onBlur"
            />
          </div>
        </div>
        <div class="form-item">
          <h2>Nº Dependentes</h2>
          <div class="input-container">
            <div ref="dependentsInputIcon">
              <i class="fa-solid fa-child" />
            </div>
            <input
              ref="dependentsInput"
              v-model="dependents"
              placeholder="0"
              @input="onInput"
              @focus="onFocus"
              @blur="onBlur"
            />
          </div>
        </div>
        <div class="form-footer">
          <button ref="btnCalcular" @click="onClickCalcular">
            <span v-if="!isBoxLoading">Calcular</span>
            <i v-if="isBoxLoading" class="fas fa-spinner" />
          </button>
        </div>
      </div>
      <div v-else ref="divContentFormResult" class="form">
        <div class="form-item">
          <h2>Salário PJ</h2>
          <div class="input-container disabled">
            <div class="focused">
              <i class="fa-solid fa-sack-dollar" />
            </div>
            <input :value="pjPay" class="focused" disabled />
          </div>
        </div>
        <div class="form-footer result">
          <button ref="btnReset" @click="onClickIconReset">
            <i class="fa-solid fa-arrows-rotate" />
          </button>
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
  transition: all 0.2s ease-in-out;

  &.hidden {
    opacity: 0.2;
  }

  .header {
    position: absolute;
    transform: translateY(-25px);
    left: calc(50% - 25px);
    width: 48px;
    height: 48px;
    background: #242424;
    border: solid 1px #42b883;
    border-radius: 100px;
    z-index: 2;

    div {
      width: calc(100% + 1px);
      height: calc(100% + 1px);
      display: flex;
      justify-content: center;
      align-items: center;

      i {
        color: #fff;
        font-size: 20px;
      }
    }
  }

  .box {
    position: relative;
    min-width: calc(200px - 22px);
    max-width: calc(300px - 22px);
    width: calc(100% - 22px);
    height: 250px;
    background: #242424;
    padding: 35px 10px 10px 10px;
    border-radius: 10px;
    border: solid 1px #42b883;
    display: flex;
    flex-direction: column;
    transition: all 0.4s ease-in-out;
    overflow: hidden;
    z-index: 1;

    &.calculated {
      height: 106px;
    }

    .form {
      display: flex;
      flex-direction: column;

      &.fade-out {
        animation: fadeOut 1s ease-out;

        @keyframes fadeOut {
          from {
            opacity: 1;
          }
          to {
            opacity: 0;
          }
        }
      }

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
          width: 85%;
          display: flex;
          justify-content: center;

          &.disabled {
            cursor: no-drop;

            input {
              cursor: no-drop;
            }
          }

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

            &.focused {
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
        flex-direction: column;
        align-items: center;
        justify-content: center;
        margin-top: 5px;

        &.result {
          display: flex;
          justify-content: center;
          align-items: center;

          button {
            width: 40px;
            font-size: 14px;
            cursor: pointer;
            background: #42b883;
            color: #1a1a1a;
            padding: 5px;
            border-radius: 50px;
            transition: all 0.2s ease-in-out;

            &:hover {
              color: #fff;
              opacity: 0.8s;
            }

            &.active {
              i {
                animation: rotation 1s ease-in-out;
              }
            }
          }

          span {
            font-size: 8px;
          }
        }

        button {
          cursor: pointer;
          width: 104px;
          padding: 5px 20px;
          border-radius: 15px;
          background: #42b883;
          color: #1a1a1a;
          font-size: 14px;
          font-weight: 800;
          transition: all 0.2s ease-in-out;

          &:hover {
            color: #fff;
            opacity: 0.8;
          }

          &.btnLoading {
            cursor: wait;
            width: 30px;
            padding: 5px 0px;
            color: #fff;
            transition: all 0.4s ease-in-out;
          }

          &.loading {
            i {
              animation: rotation 1s ease-in-out infinite;
            }
          }

          @keyframes rotation {
            from {
              transform: rotate(0deg);
            }
            to {
              transform: rotate(360deg);
            }
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
