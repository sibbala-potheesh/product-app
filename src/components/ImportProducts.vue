<script setup lang="ts">
import { ref } from "vue";
import Papa from "papaparse";
import { ArrowUpTrayIcon } from "@heroicons/vue/24/outline";

interface Product {
  id: number;
  name: string;
  price: number;
  stock: number;
  category: string;
  visible: boolean;
}

const emit = defineEmits<{
  (e: "import", products: Product[]): void;
}>();

const isImporting = ref(false);
const error = ref("");

const handleFileUpload = (event: Event) => {
  const input = event.target as HTMLInputElement;
  const file = input?.files?.[0];

  if (!file) return;

  if (!file.name.endsWith(".csv")) {
    error.value = "Please upload a CSV file";
    return;
  }

  isImporting.value = true;
  error.value = "";

  Papa.parse(file, {
    header: true,
    complete: (results) => {
      try {
        const products = results.data.map((row: any) => ({
          id: parseInt(row.id) || Math.floor(Math.random() * 10000),
          name: row.name || "",
          price: parseFloat(row.price) || 0,
          stock: parseInt(row.stock) || 0,
          category: row.category || "Uncategorized",
          visible: row.visible === "true" || true,
        }));

        emit("import", products);
        isImporting.value = false;
        if (input) input.value = "";
      } catch (e) {
        error.value = "Error processing file. Please check the format.";
        isImporting.value = false;
      }
    },
    error: (error: any) => {
      error.value = "Error reading file: " + error.message;
      isImporting.value = false;
    },
  });
};
</script>

<template>
  <div class="card p-6">
    <div class="flex items-center justify-between mb-4">
      <h2 class="text-xl font-semibold flex items-center gap-2">
        <ArrowUpTrayIcon class="w-6 h-6 text-blue-500" />
        Import Products
      </h2>
    </div>

    <div class="space-y-4">
      <div
        class="border-2 border-dashed border-gray-300 rounded-lg p-6 text-center"
      >
        <input
          type="file"
          accept=".csv"
          class="hidden"
          id="fileInput"
          @change="handleFileUpload"
        />
        <label
          for="fileInput"
          class="cursor-pointer flex flex-col items-center justify-center gap-2"
        >
          <ArrowUpTrayIcon class="w-8 h-8 text-gray-400" />
          <span class="text-gray-600">Click to upload CSV file</span>
          <span class="text-sm text-gray-500">or drag and drop</span>
        </label>
      </div>

      <Transition name="fade">
        <p v-if="error" class="text-red-500 text-sm">{{ error }}</p>
      </Transition>

      <Transition name="fade">
        <div v-if="isImporting" class="flex items-center justify-center gap-2">
          <div
            class="animate-spin rounded-full h-5 w-5 border-b-2 border-blue-500"
          ></div>
          <span class="text-gray-600">Importing products...</span>
        </div>
      </Transition>
    </div>
  </div>
</template>
