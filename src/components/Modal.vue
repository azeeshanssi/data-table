<template>
  <div
    class="w-screen h-screen bg-gray-700 bg-opacity-70 flex flex-col justify-center items-center"
    @click="handleExternalClick"
  >
    <div id="card" class="bg-white p-12 rounded-lg shadow-lg h-2/3 flex flex-col">
      <h1 class="text-center text-xl">Select Columns</h1>
      <div class="mt-2 w-full">
        <label v-for="(header, index) in dropdownOptions" :key="index" class="flex items-center">
          <input
            type="radio"
            :value="header"
            class="form-radio text-slate-600 mr-1"
            @change="handleRadioChange(header)"
          />
          {{ header }}
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
const emit = defineEmits(['close', 'add'])
const props = defineProps(['dropdown'])

//RADIO
const selected = ref([])
const dropdownOptions = ref(props.dropdown)
const handleRadioChange = (header) => {
  if (!selected.value.includes(header)) {
    selected.value.push(header)
  }
}
//MODAL
const handleAdd = () => {
  if (selected.value) {
    emit('add', selected.value)
  } else {
    console.log('Please Select a Value')
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

// LOGICAL CONCERN ORDERING
// DATA - (REF, COMPUTED, INJECTS)
// FUNCTIONS
// WATCHERS
// LIFE CYCLE HOOKS 
// PROVIDES

</script>
