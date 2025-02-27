<script setup lang="ts">
import { ref, computed, onMounted, watch } from 'vue';
import { useI18n } from 'vue-i18n';
import { useBerryStore } from '@/stores/PiniaBerry.ts';
import Pagination from './AppPagination.vue';

const { t } = useI18n();
const berryStore = useBerryStore();

const searchQuery = ref(berryStore.searchQuery);
const currentPage = ref(berryStore.currentPage);
const itemsPerPage = ref(berryStore.itemsPerPage);

onMounted(() => {
  const urlParams = new URLSearchParams(window.location.search);
  const page = urlParams.get('page');
  const perPage = urlParams.get('perPage');
  const search = urlParams.get('search');

  if (page) {
    currentPage.value = parseInt(page);
    berryStore.setPage(currentPage.value);
  }

  if (perPage) {
    itemsPerPage.value = parseInt(perPage);
    berryStore.setItemsPerPage(itemsPerPage.value);
  }

  if (search) {
    searchQuery.value = search;
    berryStore.setSearchQuery(searchQuery.value);
  }

  berryStore.fetchBerries();
});

watch([currentPage, itemsPerPage, searchQuery], () => {
  const params = new URLSearchParams();
  params.set('page', currentPage.value.toString());
  params.set('perPage', itemsPerPage.value.toString());

  if (searchQuery.value) {
    params.set('search', searchQuery.value);
  }

  const newUrl = `${window.location.pathname}?${params.toString()}`;
  window.history.pushState({}, '', newUrl);

  berryStore.setPage(currentPage.value);
  berryStore.setItemsPerPage(itemsPerPage.value);
  berryStore.setSearchQuery(searchQuery.value);
});

const berries = computed(() => berryStore.paginatedBerries);
const totalPages = computed(() => berryStore.totalPages);
const loading = computed(() => berryStore.loading);

const onPageChange = (page: number) => {
  currentPage.value = page;
  berryStore.setPage(page);
};

const onItemsPerPageChange = (items: number) => {
  itemsPerPage.value = items;
  berryStore.setItemsPerPage(items);
};

const getBerryId = (url: string): number => {
  const matches = url.match(/\/(\d+)\/$/);
  return matches ? parseInt(matches[1]) : 1;
};

const deleteBerry = (name: string) => {
  if (confirm(t('table.confirmDelete'))) {
    berryStore.deleteBerry(name);
  }
};
</script>

<template>
  <div class="w-full">
    <div class="flex justify-between items-center mb-4">
      <h2 class="text-xl font-bold">{{ t('table.title') }}</h2>
      <router-link :to="{ name: 'AddProduct' }" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">
        {{ t('table.addButton') }}
      </router-link>
    </div>

    <div class="mb-4">
      <input
        v-model="searchQuery"
        type="text"
        :placeholder="t('table.search')"
        class="w-full p-2 border border-gray-300 rounded"
      />
    </div>

    <div class="overflow-x-auto bg-white rounded-lg shadow">
      <table class="min-w-full divide-y divide-gray-200">
        <thead class="bg-gray-50">
        <tr>
          <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
            {{ t('table.number') }}
          </th>
          <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
            {{ t('table.name') }}
          </th>
          <th scope="col" class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
            {{ t('table.actions') }}
          </th>
        </tr>
        </thead>
        <tbody class="bg-white divide-y divide-gray-200">
        <template v-if="loading">
          <tr v-for="i in itemsPerPage" :key="`skeleton-${i}`">
            <td colspan="3" class="px-6 py-4">
              <div class="flex items-center">
                <div class="w-full">
                  <div class="h-4 bg-gray-200 rounded w-full"></div>
                </div>
              </div>
            </td>
          </tr>
        </template>
        <template v-else-if="berries.length === 0">
          <tr>
            <td colspan="3" class="px-6 py-4 text-center text-gray-500">
              {{ t('common.noData') }}
            </td>
          </tr>
        </template>
        <template v-else>
          <tr v-for="(berry, index) in berries" :key="berry.name">
            <td class="px-6 py-4 whitespace-nowrap">
              {{ (currentPage - 1) * itemsPerPage + index + 1 }}
            </td>
            <td class="px-6 py-4 whitespace-nowrap capitalize">
              {{ berry.name }}
            </td>
            <td class="px-6 py-4 whitespace-nowrap flex space-x-2">
              <router-link :to="{ name: 'Detail', params: { name: berry.name } }" class="text-blue-600 hover:text-blue-900">
                {{ t('table.detail') }}
              </router-link>
              <router-link :to="{ name: 'EditProduct', params: { id: getBerryId(berry.url) } }" class="text-indigo-600 hover:text-indigo-900">
                {{ t('table.edit') }}
              </router-link>
              <button @click="deleteBerry(berry.name)" class="text-red-600 hover:text-red-900">
                {{ t('table.delete') }}
              </button>
            </td>
          </tr>
        </template>
        </tbody>
      </table>
    </div>

    <Pagination
      :current-page="currentPage"
      :total-pages="totalPages"
      :items-per-page="itemsPerPage"
      :items-per-page-options="[10, 30, 50]"
      @page-change="onPageChange"
      @items-per-page-change="onItemsPerPageChange"
    />
  </div>
</template>
