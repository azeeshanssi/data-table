<template>
  <div
    class="w-screen h-screen bg-gray-700 bg-opacity-70 flex flex-col justify-center items-center"
    @click="handleExternalClick"
  >
    <div id="card" class="bg-white p-12 rounded-lg shadow-lg h-90 flex flex-col">
      <h1 class="text-center text-xl">Select Columns</h1>
      <div class="mt-2 w-full">
         <label v-for="(header, index) in dropdownOptions" :key="index" class="flex items-center capitalize">
          <input
            type="checkbox"
            :value="header"
            :checked="selected.includes(header)"
            class="  text-slate-600 mr-1 "
            @change="handleRadioChange(header)"
          />
          {{ header.replaceAll('_',' ') }}
        </label> 
      
      </div>

      <button
        @click="handleAdd"
        class="p-2 mb-1 mt-4 mx-1 rounded-lg text-white bg-blue-500 hover:bg-blue-600 transition duration-300 ease-in-out"
      >
        Add
      </button>
      <button
        @click="handleClose"
        class="p-2 m-1 rounded-lg text-white bg-red-500 hover:bg-red-600"
      >
        Cancel
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { defineEmits, defineProps } from 'vue'
import { onMounted } from 'vue';

const emit = defineEmits(['close', 'add', 'update:selected'])
const props = defineProps(['filtered','allHeaders','selected'])

// RADIO
const selected = ref([])
const dropdownOptions = ref(props.allHeaders)
dropdownOptions.value = dropdownOptions.value.filter(header => header !== "id")

// const handleRadioChange = (header) => {
//   // if (selected.value.includes(header)) {
//   //   // If the header is already in the selected array, remove it when unchecking
//   //   selected.value = selected.value.filter(item => item !== header)
//   // } else {
//   //   // If the header is not in the selected array, add it when checking
//   //   selected.value.push(header)
//   // }
  
// }
const handleRadioChange = (header) => {
  const index = selected.value.indexOf(header);
  if (index === -1) {
    // Header not found in the selected array, add it
    selected.value.push(header);
  } else {
    // Header found in the selected array, remove it
    selected.value.splice(index, 1);
  }
};
// MODAL
const handleAdd = () => {
  if (selected.value.length > 0) {
    emit('add', selected.value)
    emit('update:selected', selected.value)
  } else {
    console.log('Please Select at least one column')
  }
}

const handleClose = () => {
  emit('close')
}

const handleExternalClick = (event) => {
  if (event.target === event.currentTarget) {
    emit('close')
  }
}

// PRESELECTED HEADERS (Initialize selected array based on the provided prop)
onMounted(() => {
  selected.value = props.selected
})
</script>