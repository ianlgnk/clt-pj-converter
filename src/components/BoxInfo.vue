<script setup lang="ts">
import { ref, defineExpose } from "vue";

export interface IboxConverterInfo {
  benefits: string;
  dependents: string;
  fgts: string;
  grossPay: string;
  inss: string;
  ir: string;
  netSalary: string;
  pjSalary: string;
  thirtheenth: string;
  vacation: string;
  inssPercent: string;
  irPercent: string;
}

const isVisible = ref(false);
const data = ref<IboxConverterInfo>({
  benefits: "",
  dependents: "",
  fgts: "",
  grossPay: "",
  inss: "",
  ir: "",
  netSalary: "",
  pjSalary: "",
  thirtheenth: "",
  vacation: "",
  inssPercent: "",
  irPercent: "",
});
const parentDiv = ref<HTMLElement | null>(null);

function open(boxConverterInfo: IboxConverterInfo | undefined) {
  isVisible.value = true;
  parentDiv.value?.classList.add("visible");
  if (boxConverterInfo) data.value = boxConverterInfo;
  else
    data.value = {
      benefits: "",
      dependents: "",
      fgts: "",
      grossPay: "",
      inss: "",
      ir: "",
      netSalary: "",
      pjSalary: "",
      thirtheenth: "",
      vacation: "",
      inssPercent: "",
      irPercent: "",
    };
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
  <div ref="parentDiv" class="container">
    <div class="header">
      <h1>Cálculos</h1>
    </div>
    <div class="body">
      <div class="data-container">
        <div class="data-info">
          <div><i class="fa-solid fa-sack-dollar" /></div>
          Salário bruto
        </div>
        <span>{{ data.grossPay ? data.grossPay : "?" }}</span>
      </div>
      <div class="data-container">
        <div class="data-info">
          <div><i class="fa-solid fa-money-bills" /></div>
          Salário líquido
        </div>
        <span>{{ data.netSalary ? data.netSalary : "?" }}</span>
      </div>
      <div class="data-container">
        <div class="data-info">
          <div><i class="fa-solid fa-coins" /></div>
          Salário PJ
        </div>
        <span>{{ data.pjSalary ? data.pjSalary : "?" }}</span>
      </div>
      <div class="data-container">
        <div class="data-info">
          <div><i class="fa-solid fa-credit-card" /></div>
          Benefícios
        </div>
        <span>{{ data.benefits ? data.benefits : "?" }}</span>
      </div>
      <div class="data-container">
        <div class="data-info">
          <div><i class="fa-solid fa-child" /></div>
          Nº dependentes
        </div>
        <span>{{ data.dependents ? data.dependents : "?" }}</span>
      </div>
      <div class="data-container">
        <div class="data-info">
          <div><i class="fa-solid fa-percent" /></div>
          INSS {{ data.inssPercent }}
        </div>
        <span>{{ data.inss ? data.inss : "?" }}</span>
      </div>
      <div class="data-container">
        <div class="data-info">
          <div><i class="fa-solid fa-percent" /></div>
          IR {{ data.irPercent }}
        </div>
        <span>{{ data.ir ? data.ir : "?" }}</span>
      </div>
      <div class="data-container">
        <div class="data-info">
          <div><i class="fa-solid fa-percent" /></div>
          FGTS (8%)
        </div>
        <span>{{ data.fgts ? data.fgts : "?" }}</span>
      </div>
      <div class="data-container">
        <div class="data-info">
          <div><i class="fa-solid fa-calendar-day" /></div>
          Décimo Terceiro
        </div>
        <span>{{ data.thirtheenth ? data.thirtheenth : "?" }}</span>
      </div>
      <div class="data-container">
        <div class="data-info">
          <div><i class="fa-solid fa-umbrella-beach" /></div>
          Férias
        </div>
        <span>{{ data.vacation ? data.vacation : "?" }}</span>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.container {
  position: absolute;
  top: 0;
  padding: 10px 12px 6px 10px;
  width: calc(100vw - 24px);
  max-width: 400px;
  height: fit-content;
  transform: translateY(calc(-100vh - 110px));
  background: #242424;
  transition: all 0.2s ease-in-out;
  border-bottom-left-radius: 15px;
  border-bottom-right-radius: 15px;
  border: 1px #42b883 solid;
  z-index: 3;

  .header {
    margin-bottom: 20px;

    h1 {
      text-align: center;
      font-size: 24px;
      font-weight: 800;
    }
  }

  .body {
    display: flex;
    flex-direction: column;
    font-size: 14px;

    .data-container {
      display: flex;
      width: 100%;
      justify-content: space-between;
      margin-bottom: 5px;

      .data-info {
        display: flex;
        justify-content: center;
        align-items: center;

        div {
          width: 20px;
          display: flex;
          justify-content: center;
          margin-right: 4px;
        }
      }

      &:last-child {
        margin-bottom: 0;
      }
    }
  }

  &.visible {
    transform: translateY(0);
  }
}
</style>
