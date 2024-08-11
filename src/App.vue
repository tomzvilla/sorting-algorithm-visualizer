<script setup>
import { reactive, ref } from 'vue'
import Bars from './components/Bars.vue'
import Slider from '@vueform/slider'
import '@vueform/slider/themes/default.css'

const sortingAlgorithms = [
  { name: 'Bubble Sort', value: 'bubbleSort' },
  { name: 'Selection Sort', value: 'selectionSort' },
  { name: 'Quick Sort', value: 'quickSort' },
  { name: 'Heap Sort', value: 'heapSort' },
  { name: 'Insertion Sort', value: 'insertionSort' }
]

const selectedAlgorithm = ref('bubbleSort')
const speed = ref(50)
const arraySize = ref(20)
</script>

<template>
  <main>
    <h1>Sort Algorithm Visualizer</h1>
    <div class="optionsContainer">
      <div class="group">
        <label for="sortingAlgorithm">Sorting Algorithm </label>
        <select name="sortingAlgorithm" v-model="selectedAlgorithm">
          <option v-for="item in sortingAlgorithms" :value="item.value" :key="item.value">
            {{ item.name }}
          </option>
        </select>
      </div>
      <div class="group">
        <label for="speed">Sorting speed (in ms) </label>
        <input v-model="speed" name="speed" type="number" default="100" />
      </div>
      <div class="group">
        <label for="arraySize"> Array Size </label>
        <Slider id="slider" name="arraySize" v-model="arraySize" :min="2" :max="100" />
      </div>
    </div>
    <!-- Sort Algorithm Visualizer -->
    <Bars :arraySize="arraySize" :speed="speed" />
  </main>
</template>

<style scoped>
main {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 0 auto;
}

.optionsContainer {
  display: flex;
  flex-direction: column;
  width: 500px;
  align-items: flex-start;
  justify-content: center;
}

.group {
  width: 100%;
  margin-top: 1rem;
}

.group:last-child {
  margin-bottom: 5rem;
}

#slider {
  margin-top: 1rem;
}

.group > input {
  width: 100%;
  height: 2rem;
  margin-top: 0.5rem;
}

.group > select {
  margin-top: 0.5rem;
  width: 100%;
  height: 2rem;
}
</style>
