<template>
  <div class="overflow-x-auto w-full">
    <table class="w-full">
      <thead>
        <tr>
          <th
            class="bg-blue-600 hover:bg-blue-300 capitalize hover:cursor-pointer text-white border px-4 py-2"
            v-for="header in selectedHeaders"
            :key="header"
            @click="sortData(header)"
          >
            {{ formatHeader(header) }}
            <svg
              :class="{ 'transform rotate-180': sortBy === header && sortOrder[header] === 'desc' }"
              xmlns="http://www.w3.org/2000/svg"
              class="h-5 w-5 text-gray-600 ml-2"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M19 9l-7 7-7-7"
              />
            </svg>
          </th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(coin, coinIndex) in displayData"
          :key="coinIndex"
          :class="[{ 'bg-blue-100': coinIndex % 2 === 0 }, 'hover:bg-blue-300']"
        >
          <td
            v-for="header in selectedHeaders"
            :key="header"
            class="border px-4 py-2 text-center"
          >
            <template v-if="coin[header] != null">
              <template v-if="header === 'image'">
                <img :src="coin[header]" alt="Coin Image" class="w-10 h-10" />
              </template>
              <template v-else>
                <template v-if="(typeof coin[header]!='string' &&header.includes('change'))">
                  <template v-if="coin[header]<0">
                    <div class="text-red-400">{{ coin[header] }}</div>
                  </template>
                  <template v-else>
                    <div class="text-green-600">{{ coin[header] }}</div>
                  </template>
                </template>
                <template v-else>
                  {{coin[header]}}
                </template>
              </template>
          
            </template>
            <template v-else>
              <div class="text-center">-</div>
            </template>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
  </template>
  
  <script setup>
  import { ref } from 'vue';
  
  const props = defineProps(['selectedHeader', 'printData']);
  const selectedHeaders = ref(props.selectedHeader);
  const displayData = ref(props.printData);
  const coins = ref([]);
  coins.value = displayData.value;
  
  // Formatting logical concern
  const formatHeader = (header) => {
    const words = header.split('_').map((word) => {
      return word.charAt(0).toUpperCase() + word.slice(1);
    });
    return words.join(' ');
  };
  
  // Sorting logical concern
  const sortBy = ref(null);
  const sortOrder = ref({}); // Keep track of the sorting order for each header
  
  const sortData = (header) => {
    if (sortBy.value === header) {
      sortOrder.value[header] = sortOrder.value[header] === 'asc' ? 'desc' : 'asc';
    } else {
      sortOrder.value[header] = 'asc';
      sortBy.value = header;
    }
  
    displayData.value = [...coins.value].sort((a, b) => {
      const valueA = a[header];
      const valueB = b[header];
  
      if (typeof valueA === 'string' && typeof valueB === 'string') {
        return sortOrder.value[header] === 'asc'
          ? valueA.localeCompare(valueB)
          : valueB.localeCompare(valueA);
      } else {
        return sortOrder.value[header] === 'asc' ? valueA - valueB : valueB - valueA;
      }
    });
  };
  </script>
  