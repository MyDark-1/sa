<template>
  <div class="calculator">
    <h1 class="calculator__title">Калькулятор подсетей</h1>

    <div class="calculator__form">
      <UiField label="IP адрес">
        <UiInput v-model="ipAddress" placeholder="192.168.1.150" @keyup.enter="calculate" />
      </UiField>

      <UiField label="Маска подсети">
        <UiSelect v-model="selectedMask" :options="maskOptions" />
      </UiField>

      <UiButton layout="primary" :isDisabled="!isValidIp" @click="calculate" class="calculator__button">
        Рассчитать
      </UiButton>
    </div>

    <div v-if="results" class="calculator__results">
      <h2 class="calculator__results-title">Результаты:</h2>

      <div class="calculator__result-item"><strong>IP адрес:</strong> {{ results.ip }}</div>

      <div class="calculator__result-item"><strong>Маска:</strong> {{ results.mask }}</div>

      <div class="calculator__result-item"><strong>Адрес сети:</strong> {{ results.networkAddress }}</div>

      <div class="calculator__result-item"><strong>Количество адресов:</strong> {{ results.addressesCount }}</div>
    </div>

    <div v-if="ipAddress && !isValidIp" class="calculator__error">Неверный формат IP адреса</div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import { UiButton, UiInput, UiField, UiSelect } from 'homyevgen';
import { MASK_OPTIONS } from '../constant/mask';
import { isIpValid, getNetworkAddress, getAddressesCount } from '../utils/networkCalculations';

interface CalculationResults {
  ip: string;
  mask: string;
  networkAddress: string;
  addressesCount: number;
}

const ipAddress = ref('');
const selectedMask = ref('24/255.255.255.0');
const results = ref<CalculationResults | null>(null);

const maskOptions = MASK_OPTIONS.map((option) => option.label);

const isValidIp = computed(() => isIpValid(ipAddress.value));

const calculate = () => {
  if (!isValidIp.value) return;

  const selectedMaskData = MASK_OPTIONS.find((option) => option.label === selectedMask.value);

  if (!selectedMaskData) return;

  const networkAddress = getNetworkAddress(ipAddress.value, selectedMaskData.value);
  const addressesCount = getAddressesCount(selectedMaskData.value);

  results.value = {
    ip: ipAddress.value,
    mask: selectedMask.value,
    networkAddress,
    addressesCount,
  };
};
</script>

<style scoped>
.calculator {
  max-width: 500px;
  margin: 0 auto;
  padding: 24px;
  background-color: var(--color-background);
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.calculator__title {
  margin: 0 0 24px 0;
  text-align: center;
  color: var(--color-text);
  font-size: 24px;
  font-weight: 600;
}

.calculator__form {
  display: flex;
  flex-direction: column;
  gap: 16px;
  margin-bottom: 24px;
}

.calculator__button {
  margin-top: 8px;
}

.calculator__results {
  padding: 16px;
  background-color: var(--color-white);
  border-radius: 8px;
  border: 1px solid var(--color-border);
}

.calculator__results-title {
  margin: 0 0 12px 0;
  color: var(--color-text);
  font-size: 18px;
  font-weight: 600;
}

.calculator__result-item {
  padding: 8px 0;
  border-bottom: 1px solid var(--color-border);
  color: var(--color-text);
}

.calculator__result-item:last-child {
  border-bottom: none;
}

.calculator__error {
  margin-top: 16px;
  padding: 12px;
  background-color: #fee;
  border: 1px solid #fcc;
  border-radius: 6px;
  color: #c33;
  text-align: center;
}
</style>
