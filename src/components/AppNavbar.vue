<script setup lang="ts">
import { ref } from 'vue';
import { useI18n } from 'vue-i18n';

const { locale } = useI18n();
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
        <img src="/favicon.ico" alt="Pokéball" class="h-8 w-8" />
        <router-link :to="{ name: 'Home' }" class="text-xl font-bold">
          Pokéberry
        </router-link>
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
