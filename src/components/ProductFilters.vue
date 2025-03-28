<script setup lang="ts">
import { ref } from "vue";
import {
  FunnelIcon,
  CurrencyDollarIcon,
  ArchiveBoxIcon,
  XCircleIcon,
  ArrowPathIcon,
  TagIcon,
} from "@heroicons/vue/24/outline";

const emit = defineEmits<{
  (e: "filter", filters: any): void;
}>();

const priceRange = ref<{ min: number | null; max: number | null }>({
  min: null,
  max: null,
});
const stockRange = ref<{ min: number | null; max: number | null }>({
  min: null,
  max: null,
});
const selectedCategories = ref<string[]>([]);
const showLowStock = ref(false);
const showOutOfStock = ref(false);

const availableCategories = ref(["Electronics", "Shoes", "Home & Kitchen"]);

const updateFilters = () => {
  emit("filter", {
    priceRange: priceRange.value,
    stockRange: stockRange.value,
    categories: selectedCategories.value,
    showLowStock: showLowStock.value,
    showOutOfStock: showOutOfStock.value,
  });
};

const resetFilters = () => {
  priceRange.value = { min: null, max: null };
  stockRange.value = { min: null, max: null };
  selectedCategories.value = [];
  showLowStock.value = false;
  showOutOfStock.value = false;
  updateFilters();
};
</script>

<template>
  <div class="card p-6 space-y-6">
    <div class="flex items-center justify-between">
      <div class="flex items-center gap-2">
        <FunnelIcon class="w-6 h-6 text-blue-500" />
        <h2 class="text-xl font-semibold">Filters</h2>
      </div>
      <button
        class="btn bg-gray-500 hover:bg-gray-600 !py-1"
        @click="resetFilters"
      >
        <ArrowPathIcon class="w-4 h-4" />
        Reset
      </button>
    </div>

    <div class="space-y-6">
      <div>
        <div class="flex items-center gap-2 mb-2">
          <CurrencyDollarIcon class="w-5 h-5 text-gray-500" />
          <h3 class="text-sm font-medium text-gray-700">Price Range</h3>
        </div>
        <div class="flex space-x-2">
          <input
            v-model.number="priceRange.min"
            type="number"
            placeholder="Min"
            class="input w-full"
            min="0"
            @change="updateFilters"
          />
          <input
            v-model.number="priceRange.max"
            type="number"
            placeholder="Max"
            class="input w-full"
            min="0"
            @change="updateFilters"
          />
        </div>
      </div>

      <div>
        <div class="flex items-center gap-2 mb-2">
          <ArchiveBoxIcon class="w-5 h-5 text-gray-500" />
          <h3 class="text-sm font-medium text-gray-700">Stock Range</h3>
        </div>
        <div class="flex space-x-2">
          <input
            v-model.number="stockRange.min"
            type="number"
            placeholder="Min"
            class="input w-full"
            min="0"
            @change="updateFilters"
          />
          <input
            v-model.number="stockRange.max"
            type="number"
            placeholder="Max"
            class="input w-full"
            min="0"
            @change="updateFilters"
          />
        </div>
      </div>

      <div class="space-y-3">
        <div class="flex items-center gap-2">
          <XCircleIcon class="w-5 h-5 text-gray-500" />
          <h3 class="text-sm font-medium text-gray-700">Stock Status</h3>
        </div>
        <label class="flex items-center space-x-2 cursor-pointer">
          <input
            type="checkbox"
            v-model="showLowStock"
            @change="updateFilters"
            class="rounded text-blue-500 focus:ring-blue-500"
          />
          <span class="text-gray-700">Show Low Stock Only</span>
        </label>
        <label class="flex items-center space-x-2 cursor-pointer">
          <input
            type="checkbox"
            v-model="showOutOfStock"
            @change="updateFilters"
            class="rounded text-blue-500 focus:ring-blue-500"
          />
          <span class="text-gray-700">Show Out of Stock Only</span>
        </label>
      </div>

      <div class="space-y-3">
        <div class="flex items-center gap-2">
          <TagIcon class="w-5 h-5 text-gray-500" />
          <h3 class="text-sm font-medium text-gray-700">Categories</h3>
        </div>
        <div class="space-y-2">
          <label
            v-for="category in availableCategories"
            :key="category"
            class="flex items-center space-x-2 cursor-pointer"
          >
            <input
              type="checkbox"
              :value="category"
              v-model="selectedCategories"
              @change="updateFilters"
              class="rounded text-blue-500 focus:ring-blue-500"
            />
            <span class="text-gray-700">{{ category }}</span>
          </label>
        </div>
      </div>
    </div>
  </div>
</template>
