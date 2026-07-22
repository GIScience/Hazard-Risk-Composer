<script setup lang="ts">
import { ref, onMounted } from "vue";
import { DataSourcesURL } from "@/config";

interface DataSource {
  name: string;
  description: string;
  url: string | null;
}

const data = ref<DataSource[]>([]);
const isLoading = ref(false);

async function fetchDataSources() {
  isLoading.value = true;
  try {
    const response = await fetch(DataSourcesURL);
    if (!response.ok) throw new Error(`HTTP ${response.status}`);
    data.value = await response.json();
  } catch (error) {
    console.error("Error fetching data sources:", error);
  } finally {
    isLoading.value = false;
  }
}

onMounted(() => {
  fetchDataSources();
});
</script>

<template>
  <section class="h-full min-h-[400px] flex flex-col">
    <h3 class="text-lg font-extrabold text-slate-900 mb-2 mt-2 tracking-tight">
      Data Sources
    </h3>
    <div id="data-sources" class="flex-1 space-y-6 w-[43rem]">
      <!-- <div class="flex flex-wrap bg-slate-100 rounded-md shadow-sm w-fit">
        <v-btn
          v-for="(hazard, index) in HAZARDS"
          variant="outlined"
          :key="hazard.keyword"
          @click="selectedHazard = hazard.keyword"
          :class="
            cn(
              'bg-slate-100 text-slate-700 text-xs font-bold uppercase shadow-none tracking-wider transition-colors py-1 px-4',
              'rounded-none',
              index === 0 && 'rounded-s-md',
              index === HAZARDS.length - 1 && 'rounded-e-md',
              selectedHazard === hazard.keyword
                ? 'bg-heigit-red text-white'
                : 'text-slate-700 bg-none',
            )
          "
        >
          {{ hazard.label }}
        </v-btn>
      </div> -->
      <div
        class="overflow-auto border border-slate-200 rounded-lg custom-scrollbar bg-white"
      >
        <v-table
          class="w-full table-fixed rounded text-left text-sm text-slate-600 relative border-collapse"
        >
          <thead
            class="text-xs text-slate-500 uppercase sticky top-0 z-10 shadow-sm border-b border-slate-200"
          >
            <tr>
              <th
                class="w-1/4 px-4 py-2 bg-slate-50 whitespace-nowrap border-b border-slate-200"
              >
                Name
              </th>
              <th
                class="w-1/4 px-4 py-2 bg-slate-50 whitespace-nowrap border-b border-slate-200"
              >
                Description
              </th>
              <th
                class="w-1/2 px-4 py-2 bg-slate-50 whitespace-nowrap border-b border-slate-200"
              >
                Link
              </th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in data" :key="item.name">
              <td
                class="px-4 py-2 font-medium text-slate-900 break-words border-b border-slate-100"
              >
                {{ item.name }}
              </td>
              <td
                class="px-4 py-2 font-medium text-slate-900 break-words border-b border-slate-100"
              >
                {{ item.description }}
              </td>
              <td
                class="px-4 py-2 font-medium text-slate-900 border-b border-slate-100"
              >
                <a
                  v-if="item.url"
                  :href="item.url"
                  target="_blank"
                  rel="noopener noreferrer"
                  class="text-heigit-red underline break-all"
                >
                  {{ item.url }}
                </a>
                <span v-else class="text-slate-400">—</span>
              </td>
            </tr>
          </tbody>
        </v-table>
      </div>
    </div>
  </section>
</template>
