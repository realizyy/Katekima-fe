<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { useI18n } from 'vue-i18n';
import { useRouter } from 'vue-router';
import { useProductStore } from '@/stores/PiniaProduct.ts';
import type { Product } from '../types/Product.ts';
import SkeletonLoader from './AppSkelektonLoader.vue';

const { t } = useI18n();
const router = useRouter();
const productStore = useProductStore();

const props = defineProps({
  isEdit: {
    type: Boolean,
    default: false
  },
  productId: {
    type: [Number, String],
    default: null
  }
});

const formData = ref<Product>({
  title: '',
  price: 0,
  description: '',
  image: '',
  category: ''
});

onMounted(async () => {
  if (props.isEdit && props.productId) {
    const id = typeof props.productId === 'string' ? parseInt(props.productId) : props.productId;
    await productStore.fetchProduct(id);

    if (productStore.product) {
      formData.value = { ...productStore.product };
    }
  } else {
    productStore.resetProduct();
  }
});

const submitForm = async () => {
  if (props.isEdit && props.productId) {
    const id = typeof props.productId === 'string' ? parseInt(props.productId) : props.productId;
    const success = await productStore.updateProduct(id, formData.value);

    if (success) {
      // Stay on page to show success message
      setTimeout(() => {
        router.push({ name: 'TableList' });
      }, 2000);
    }
  } else {
    const success = await productStore.addProduct(formData.value);

    if (success) {
      // Reset form after successful submission
      formData.value = {
        title: '',
        price: 0,
        description: '',
        image: '',
        category: ''
      };

      // Stay on page to show success message
      setTimeout(() => {
        router.push({ name: 'TableList' });
      }, 2000);
    }
  }
};
</script>

<template>
  <form @submit.prevent="submitForm" class="space-y-6">
    <div v-if="productStore.addSuccess" class="bg-green-100 border border-green-400 text-green-700 px-4 py-3 rounded mb-4">
      {{ t(isEdit ? 'product.edit.success' : 'product.add.success') }}
    </div>

    <div v-if="productStore.loading">
      <SkeletonLoader type="form" />
    </div>

    <div v-else>
      <div class="space-y-4">
        <div>
          <label for="title" class="block text-sm font-medium text-gray-700">
            {{ t('product.form.title') }} *
          </label>
          <input
            id="title"
            v-model="formData.title"
            type="text"
            required
            :placeholder="t('product.form.titlePlaceholder')"
            class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 p-2 border"
          />
        </div>

        <div>
          <label for="price" class="block text-sm font-medium text-gray-700">
            {{ t('product.form.price') }} *
          </label>
          <input
            id="price"
            v-model.number="formData.price"
            type="number"
            required
            step="0.01"
            min="0"
            :placeholder="t('product.form.pricePlaceholder')"
            class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 p-2 border"
          />
        </div>

        <div>
          <label for="description" class="block text-sm font-medium text-gray-700">
            {{ t('product.form.description') }} *
          </label>
          <textarea
            id="description"
            v-model="formData.description"
            required
            rows="4"
            :placeholder="t('product.form.descriptionPlaceholder')"
            class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 p-2 border"
          ></textarea>
        </div>

        <div>
          <label for="image" class="block text-sm font-medium text-gray-700">
            {{ t('product.form.image') }} *
          </label>
          <input
            id="image"
            v-model="formData.image"
            type="text"
            required
            :placeholder="t('product.form.imagePlaceholder')"
            class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 p-2 border"
          />
        </div>

        <div>
          <label for="category" class="block text-sm font-medium text-gray-700">
            {{ t('product.form.category') }} *
          </label>
          <input
            id="category"
            v-model="formData.category"
            type="text"
            required
            :placeholder="t('product.form.categoryPlaceholder')"
            class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 p-2 border"
          />
        </div>
      </div>

      <div class="flex justify-end space-x-3 mt-6">
        <button
          type="button"
          @click="router.push({ name: 'TableList' })"
          class="px-4 py-2 border border-gray-300 rounded-md shadow-sm text-sm font-medium text-gray-700 bg-white hover:bg-gray-50"
        >
          {{ t('product.form.cancel') }}
        </button>
        <button
          type="submit"
          class="px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-indigo-600 hover:bg-indigo-700"
        >
          {{ t('product.form.submit') }}
        </button>
      </div>
    </div>
  </form>
</template>
