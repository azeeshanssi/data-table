<template>
       
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
</template>

<script setup>
import { ref } from 'vue';
const props = defineProps(['selectedHeader','printData']);
const selectedHeaders=ref(props.selectedHeader);
const displayData=ref(props.printData);;
const coins = ref([]);
coins.value=displayData.value;
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


</script>