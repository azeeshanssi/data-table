<template>
  <Modal v-if="showModal" :dropdown="filteredHeaders" @close="handleClose" @add="handleAdd"></Modal>
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
      <table class="w-5/6">
        <thead>
          <tr>
            <th
              class="bg-blue-600 hover:bg-blue-300 capitalize hover:cursor-pointer text-white border px-4 py-2"
              v-for="header in selectedHeaders"
              :key="header"
              @click="sortData(header)"
            >
              <!-- {{ formatHeader(header) }} -->
              {{  header.replaceAll('_',' ') }}
            </th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(coin, coinIndex) in displayData"
            :key="coinIndex"
            :class="[{ 'bg-blue-100': coinIndex % 2 === 0 }, 'hover:bg-blue-300']"
          >
            <td v-for="header in selectedHeaders" :key="header" class="border px-4 py-2">
              <template v-if="coin[header] != null">
                <template v-if="header === 'image'">
                  <img :src="coin[header]" alt="Coin Image" class="w-10 h-10" />
                </template>
                <template v-else>
                  {{ coin[header] }}
                </template>
              </template>
              <template v-else> <div class="text-center">-</div> </template>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import Modal from './components/Modal.vue'

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
//Formatting logical concern
const formatHeader = (header) => {
  const words = header.split('_').map((word) => {
    return word.charAt(0).toUpperCase() + word.slice(1)
  })
  return words.join(' ')
}

//Sorting logical concern
const sortBy = ref(null)
const sortAsc = ref(true)

const sortData = (header) => {
  if (sortBy.value === header) {
    sortAsc.value = !sortAsc.value
  } else {
    sortAsc.value = true
    sortBy.value = header
  }

  if (!sortBy.value) {
    sortedData.value = coins.value
    return
  }

  displayData.value = [...coins.value].sort((a, b) => {
    const valueA = a[header]
    const valueB = b[header]

    if (typeof valueA === 'string' && typeof valueB === 'string') {
      return sortAsc.value ? valueA.localeCompare(valueB) : valueB.localeCompare(valueA)
    } else {
      return sortAsc.value ? valueA - valueB : valueB - valueA
    }
  })
}

//Api logical concern
const displayData = ref([])

onMounted(async () => {
  try {
    // Check if data exists in local storage
    const storedData = localStorage.getItem('coinData')

    if (storedData) {
      coins.value = JSON.parse(storedData)
      localStorage.removeItem('coinData');
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
  } catch (error) {
    console.error('Error fetching data:', error)
  }
})
</script>
