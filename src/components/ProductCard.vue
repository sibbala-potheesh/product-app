<script setup lang="ts">
import { ref, computed } from "vue";
import {
  PencilSquareIcon,
  EyeIcon,
  EyeSlashIcon,
  CheckIcon,
  XMarkIcon,
  TagIcon,
  ArchiveBoxIcon,
} from "@heroicons/vue/24/outline";

interface Product {
  id: number;
  name: string;
  price: number;
  stock: number;
  category: string;
  visible: boolean;
}

const props = defineProps<{
  product: Product;
}>();

const emit = defineEmits<{
  (e: "update", product: Product): void;
}>();

const isEditing = ref(false);
const editedProduct = ref({ ...props.product });

const stockStatus = computed(() => {
  if (props.product.stock === 0)
    return {
      text: "Out of Stock",
      class: "bg-red-100 text-red-800",
      icon: XMarkIcon,
    };
  if (props.product.stock < 10)
    return {
      text: "Low Stock",
      class: "bg-yellow-100 text-yellow-800",
      icon: ArchiveBoxIcon,
    };
  if (props.product.stock <= 20)
    return {
      text: "Medium Stock",
      class: "bg-blue-100 text-blue-800",
      icon: ArchiveBoxIcon,
    };
  return {
    text: "High Stock",
    class: "bg-green-100 text-green-800",
    icon: CheckIcon,
  };
});

const saveChanges = () => {
  if (editedProduct.value.price < 0 || editedProduct.value.stock < 0) return;
  emit("update", { ...editedProduct.value });
  isEditing.value = false;
};
</script>

<template>
  <div class="card scale-hover overflow-hidden">
    <div v-if="!isEditing" class="relative">
      <div class="absolute top-4 right-4 flex items-center gap-2">
        <button
          class="icon-button-primary"
          @click="isEditing = true"
          title="Edit product"
        >
          <PencilSquareIcon class="w-5 h-5" />
        </button>
        <button
          class="icon-button-danger"
          @click="emit('update', { ...product, visible: !product.visible })"
          :title="product.visible ? 'Hide product' : 'Show product'"
        >
          <component
            :is="product.visible ? EyeSlashIcon : EyeIcon"
            class="w-5 h-5"
          />
        </button>
      </div>

      <div class="p-6">
        <div class="mb-4">
          <h3 class="text-xl font-semibold text-gray-900 mb-2">
            {{ product.name }}
          </h3>
          <div
            class="flex items-center gap-2 text-gray-900 text-lg font-medium"
          >
            <div
              class="flex items-center gap-2 text-gray-900 text-lg font-medium"
            >
              <span class="text-gray-500">₹</span>
              <span>{{ product.price.toFixed(2) }}</span>
            </div>
          </div>
        </div>

        <div class="flex items-center gap-4">
          <div class="flex items-center gap-2 text-gray-600">
            <TagIcon class="w-4 h-4" />
            <span>{{ product.category }}</span>
          </div>

          <div :class="['badge', stockStatus.class]">
            <component :is="stockStatus.icon" class="w-4 h-4" />
            {{ stockStatus.text }}
          </div>
        </div>
      </div>
    </div>

    <!-- Edit Mode -->
    <div v-else class="p-6 space-y-4">
      <div>
        <label class="label">Name</label>
        <input
          v-model="editedProduct.name"
          type="text"
          class="input w-full"
          placeholder="Product name"
        />
      </div>

      <!-- Price Field with Rupee Icon -->
      <div>
        <label class="label">Price</label>
        <div class="relative">
          <span class="absolute left-3 top-2 text-gray-500">₹</span>
          <input
            v-model="editedProduct.price"
            type="number"
            class="input w-full pl-8"
            placeholder="Product price"
          />
        </div>
      </div>

      <div>
        <label class="label">Category</label>
        <div class="relative">
          <span class="absolute left-3 top-2 text-gray-500">
            <TagIcon class="w-5 h-5" />
          </span>
          <input
            v-model="editedProduct.category"
            type="text"
            class="input w-full pl-10"
            placeholder="Product category"
          />
        </div>
      </div>

      <div>
        <label class="label">Stock</label>
        <div class="relative">
          <span class="absolute left-3 top-2 text-gray-500">
            <ArchiveBoxIcon class="w-5 h-5" />
          </span>
          <input
            v-model="editedProduct.stock"
            type="number"
            class="input w-full pl-10"
            placeholder="Stock quantity"
          />
        </div>
      </div>

      <div class="flex justify-end space-x-2 pt-2">
        <button
          class="icon-button-danger"
          @click="isEditing = false"
          title="Cancel"
        >
          <XMarkIcon class="w-6 h-6" />
        </button>
        <button
          class="icon-button-primary"
          @click="saveChanges"
          :disabled="editedProduct.price < 0 || editedProduct.stock < 0"
          title="Save changes"
        >
          <CheckIcon class="w-6 h-6" />
        </button>
      </div>
    </div>
  </div>
</template>
