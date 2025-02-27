<script setup lang="ts">
import { useI18n } from 'vue-i18n';
import { useRoute } from 'vue-router';
import ProductForm from '../components/AppForm.vue';

const { t } = useI18n();
const route = useRoute();
const productId = route.params.id;
</script>

<template>
  <div class="container mx-auto py-8 px-4">
    <h1 class="text-2xl font-bold mb-6">{{ t('product.edit.title') }}</h1>
    <div class="bg-white rounded-lg shadow-md p-6">
      <ProductForm :is-edit="true" :product-id="productId" />
    </div>
  </div>
</template>
