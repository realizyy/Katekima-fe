<script setup lang="ts">
import { ref, computed, onMounted } from 'vue';
import { useI18n } from 'vue-i18n';
import { useRoute, useRouter } from 'vue-router';
import { useBerryStore } from '../stores/PiniaBerry.ts';
import SkeletonLoader from '../components/AppSkelektonLoader.vue';

const { t } = useI18n();
const route = useRoute();
const router = useRouter();
const berryStore = useBerryStore();

const selectedBerryName = ref('');
const berries = computed(() => berryStore.berries);
const berryDetails = computed(() => berryStore.selectedBerry);
const loading = computed(() => berryStore.loading);

// Load data on component mount
onMounted(async () => {
  if (berries.value.length === 0) {
    await berryStore.fetchBerries();
  }

  const berryName = route.params.name as string;
  if (berryName) {
    selectedBerryName.value = berryName;
    await berryStore.fetchBerryDetails(berryName);
  }
});

const fetchBerryDetails = async () => {
  if (selectedBerryName.value) {
    router.push({ name: 'Detail', params: { name: selectedBerryName.value } });
    await berryStore.fetchBerryDetails(selectedBerryName.value);
  }
};
</script>

<template>
  <div class="container mx-auto py-8 px-4">
    <h1 class="text-2xl font-bold mb-6">{{ t('detail.title') }}</h1>

    <div class="bg-white rounded-lg shadow-md p-6 mb-6">
      <div class="flex flex-col md:flex-row md:items-center md:space-x-4 mb-6">
        <select
          v-model="selectedBerryName"
          class="border border-gray-300 rounded px-3 py-2 mb-2 md:mb-0 md:flex-grow"
        >
          <option v-for="berry in berries" :key="berry.name" :value="berry.name">
            {{ berry.name }}
          </option>
        </select>
        <button
          @click="fetchBerryDetails"
          class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
        >
          {{ t('detail.moveButton') }}
        </button>
      </div>

      <div v-if="loading">
        <SkeletonLoader type="detail" />
      </div>

      <div v-else-if="berryDetails">
        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <div class="bg-gray-50 p-4 rounded">
            <div class="font-bold text-gray-700">{{ t('detail.properties.id') }}</div>
            <div>{{ berryDetails.id }}</div>
          </div>

          <div class="bg-gray-50 p-4 rounded">
            <div class="font-bold text-gray-700">{{ t('detail.properties.name') }}</div>
            <div>{{ berryDetails.name }}</div>
          </div>

          <div class="bg-gray-50 p-4 rounded">
            <div class="font-bold text-gray-700">{{ t('detail.properties.growthTime') }}</div>
            <div>{{ berryDetails.growth_time }}</div>
          </div>

          <div class="bg-gray-50 p-4 rounded">
            <div class="font-bold text-gray-700">{{ t('detail.properties.maxHarvest') }}</div>
            <div>{{ berryDetails.max_harvest }}</div>
          </div>

          <div class="bg-gray-50 p-4 rounded">
            <div class="font-bold text-gray-700">{{ t('detail.properties.naturalGiftPower') }}</div>
            <div>{{ berryDetails.natural_gift_power }}</div>
          </div>

          <div class="bg-gray-50 p-4 rounded">
            <div class="font-bold text-gray-700">{{ t('detail.properties.size') }}</div>
            <div>{{ berryDetails.size }}</div>
          </div>

          <div class="bg-gray-50 p-4 rounded">
            <div class="font-bold text-gray-700">{{ t('detail.properties.smoothness') }}</div>
            <div>{{ berryDetails.smoothness }}</div>
          </div>

          <div class="bg-gray-50 p-4 rounded">
            <div class="font-bold text-gray-700">{{ t('detail.properties.soilDryness') }}</div>
            <div>{{ berryDetails.soil_dryness }}</div>
          </div>

          <div class="bg-gray-50 p-4 rounded">
            <div class="font-bold text-gray-700">{{ t('detail.properties.firmness') }}</div>
            <div>{{ berryDetails.firmness.name }}</div>
          </div>

          <div class="bg-gray-50 p-4 rounded">
            <div class="font-bold text-gray-700">{{ t('detail.properties.item') }}</div>
            <div>{{ berryDetails.item.name }}</div>
          </div>

          <div class="bg-gray-50 p-4 rounded">
            <div class="font-bold text-gray-700">{{ t('detail.properties.naturalGiftType') }}</div>
            <div>{{ berryDetails.natural_gift_type.name }}</div>
          </div>
        </div>
      </div>

      <div v-else class="text-center py-8 text-gray-500">
        {{ t('common.noData') }}
      </div>
    </div>
  </div>
</template>
