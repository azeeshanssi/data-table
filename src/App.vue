<template>
  <Modal v-if="showModal" :filtered="filteredHeaders" :allHeaders="headers" :selected="selectedHeaders" @update:selected="updateSelected" @close="handleClose" @add="handleAdd"></Modal>
  <div v-if="!showModal" class="w-screen h-screen flex flex-col items-center">
    <h1 class="text-3xl font-medium my-2">Crypto Currency Data</h1>
    <div class="w-full flex flex-col items-center">
      <h1 class="text-3xl font-medium my-2">My Data Table</h1>
      <button
        @click="setModal"
        class="px-4 py-2 rounded-md bg-blue-500 hover:bg-blue-600 mt-4 text-white mb-2"
      >
        Add Column
      </button>
    <Table v-if="isMounted" :selected-header="selectedHeaders" :print-data="displayData"></Table>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import Modal from './components/Modal.vue'
import Table from './components/Table.vue';

//Display logical concern

const coins = ref([])
const headers = ref([])
const addColumn = ref(null)
const selectedHeaders = ref([
  'image',
  'name',
  'symbol',
  'current_price',
  'market_cap',
  'price_change_percentage_24h'
])
const excludedHeaders = ref([
  'image',
  'id',
  'name',
  'symbol',
  'current_price',
  'market_cap',
  'price_change_percentage_24h'
])
const filteredHeaders = ref([])
//Modal Logical Concern
const showModal = ref(false)
const setModal = () => {
  showModal.value = true
}

const handleClose = () => {
  showModal.value = false
}

const handleAdd = (selected) => {
  addColumn.value = selected
  showModal.value = false
  selectedHeaders.value = selectedHeaders.value.concat(addColumn.value)
  for (const value of addColumn.value) {
    const columnIndex = filteredHeaders.value.indexOf(value)
    if (columnIndex !== -1) {
      filteredHeaders.value.splice(columnIndex, 1)
    }
  }
}
const updateSelected = (value) => {
  selectedHeaders.value=value;
}
const isMounted = ref(false)

//API
const displayData = ref([])

onMounted(async () => {
  try {
    // Check if data exists in local storage
    const storedData = localStorage.getItem('coinData')

    if (storedData) {
      coins.value = JSON.parse(storedData)
    } else {
      const response = await fetch(
        'https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false'
      )

      if (!response.ok) {
        throw new Error('Network response was not ok')
      }

      const data = await response.json();
    
      coins.value = data;

      localStorage.setItem('coinData', JSON.stringify(data));
    }

    headers.value = coins.value.length > 0 ? Object.keys(coins.value[0]) : []
    filteredHeaders.value = headers.value.filter(
      (header) => !excludedHeaders.value.includes(header)
    )

    displayData.value = coins.value
    isMounted.value = true; // Indicate that onMounted has completed
  } catch (error) {
    console.error('Error fetching data:', error)
  }
})
</script>
