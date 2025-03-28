<script setup lang="ts">
import { ref, computed } from "vue";
import ProductCard from "./components/ProductCard.vue";
import ProductFilters from "./components/ProductFilters.vue";
import ImportProducts from "./components/ImportProducts.vue";
import { ShoppingBagIcon } from "@heroicons/vue/24/outline";

interface Product {
  id: number;
  name: string;
  price: number;
  stock: number;
  category: string;
}

const products = ref<Product[]>([
  {
    id: 1,
    name: "Laptop Pro",
    price: 1299.99,
    stock: 5,
    category: "Electronics",
  },
  {
    id: 2,
    name: "Wireless Mouse",
    price: 29.99,
    stock: 15,
    category: "Electronics",
  },
  {
    id: 3,
    name: "4K Monitor",
    price: 499.99,
    stock: 0,
    category: "Electronics",
  },
  {
    id: 4,
    name: "Nike Sneakers",
    price: 89.99,
    stock: 8,
    category: "Shoes",
  },
  {
    id: 5,
    name: "Office Chair",
    price: 199.99,
    stock: 12,
    category: "Home & Kitchen",
  },
  {
    id: 6,
    name: "Smartphone",
    price: 999.99,
    stock: 10,
    category: "Electronics",
  },
  {
    id: 7,
    name: "Bluetooth Speaker",
    price: 79.99,
    stock: 25,
    category: "Home & Kitchen",
  },
]);

const filters = ref({
  priceRange: { min: null, max: null },
  stockRange: { min: null, max: null },
  categories: [] as string[],
  showLowStock: false,
  showOutOfStock: false,
});

const filteredProducts = computed(() => {
  return products.value.filter((product) => {
    const { priceRange, stockRange, categories, showLowStock, showOutOfStock } =
      filters.value;

    const minStock = stockRange.min !== "" ? stockRange.min : null;
    const maxStock = stockRange.max !== "" ? stockRange.max : null;

    const matchesPrice =
      (priceRange.min === null || product.price >= priceRange.min) &&
      (priceRange.max === null || product.price <= priceRange.max);

    const matchesStock =
      (minStock === null || product.stock >= minStock) &&
      (maxStock === null || product.stock <= maxStock);

    const matchesStockStatus =
      (!showLowStock && !showOutOfStock) ||
      (showLowStock && product.stock < 10 && product.stock > 0) ||
      (showOutOfStock && product.stock === 0);

    const matchesCategory =
      categories.length === 0 || categories.includes(product.category);

    return (
      matchesPrice && matchesStock && matchesStockStatus && matchesCategory
    );
  });
});

const updateProduct = (updatedProduct: Product) => {
  const index = products.value.findIndex((p) => p.id === updatedProduct.id);
  if (index !== -1) {
    products.value[index] = updatedProduct;
  }
};

const updateFilters = (newFilters: any) => {
  filters.value = { ...filters.value, ...newFilters };
};

const handleImport = (importedProducts: Product[]) => {
  products.value = [...products.value, ...importedProducts];
};
</script>

<template>
  <div class="min-h-screen bg-gradient-to-br from-gray-50 to-gray-100 p-8">
    <div class="max-w-7xl mx-auto">
      <div class="flex items-center gap-3 mb-8">
        <div class="bg-white p-2 rounded-lg shadow-sm">
          <ShoppingBagIcon class="w-8 h-8 text-blue-500" />
        </div>
        <h1
          class="text-3xl font-bold bg-gradient-to-r from-blue-600 to-blue-800 bg-clip-text text-transparent"
        >
          Product Management
        </h1>
      </div>

      <div class="grid grid-cols-4 gap-6">
        <div class="col-span-1 space-y-6">
          <ProductFilters @filter="updateFilters" />
          <ImportProducts @import="handleImport" />
        </div>

        <div class="col-span-3">
          <TransitionGroup
            name="slide-fade"
            tag="div"
            class="grid grid-cols-1 md:grid-cols-2 gap-6"
          >
            <ProductCard
              v-for="product in filteredProducts"
              :key="product.id"
              :product="product"
              @update="updateProduct"
            />
          </TransitionGroup>
        </div>
      </div>
    </div>
  </div>
</template>
