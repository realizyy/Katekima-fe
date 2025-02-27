<script setup lang="ts">
import { ref } from 'vue';
import { useI18n } from 'vue-i18n';

const { t, locale } = useI18n();
const currentLocale = ref(locale.value);

const changeLocale = () => {
  locale.value = currentLocale.value;
  localStorage.setItem('locale', currentLocale.value);
};
</script>

<template>
  <nav class="bg-gray-800 text-white p-4">
    <div class="container mx-auto flex justify-between items-center">
      <div class="flex items-center space-x-6">
<!--        <router-link :to="{ name: 'TableList' }" class="text-xl font-bold">-->
<!--          Berry App-->
<!--        </router-link>-->
<!--        <div class="hidden md:flex space-x-4">-->
<!--          <router-link :to="{ name: 'TableList' }" class="hover:text-gray-300">-->
<!--            {{ t('navbar.table') }}-->
<!--          </router-link>-->
<!--        </div>-->
      </div>

      <div class="flex items-center space-x-4">
        <select
          v-model="currentLocale"
          @change="changeLocale"
          class="bg-gray-700 text-white px-2 py-1 rounded"
        >
          <option value="en">English</option>
          <option value="id">Indonesia</option>
        </select>
      </div>
    </div>
  </nav>
</template>
