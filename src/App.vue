<script setup>
import { ref } from 'vue'
import Bars from './components/Bars.vue'
import Slider from '@vueform/slider'
import '@vueform/slider/themes/default.css'

const sortingAlgorithms = [
  { name: 'Bubble Sort', value: 'bubbleSort', group: 'quadratic' },
  { name: 'Insertion Sort', value: 'insertionSort', group: 'quadratic' },
  { name: 'Selection Sort', value: 'selectionSort', group: 'quadratic' },
  { name: 'Quick Sort', value: 'quickSort', group: 'logarithmic' },
  { name: 'Heap Sort', value: 'heapSort', group: 'logarithmic' },
  { name: 'Merge Sort', value: 'mergeSort', group: 'logarithmic' }
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
        <select id="sortingAlgorithm" v-model="selectedAlgorithm">
          <optgroup label="Quadratic">
            <option
              v-for="item in sortingAlgorithms.filter((alg) => alg.group === 'quadratic')"
              :value="item.value"
              :key="item.value"
            >
              {{ item.name }}
            </option>
          </optgroup>
          <optgroup label="Logarithmic">
            <option
              v-for="item in sortingAlgorithms.filter((alg) => alg.group === 'logarithmic')"
              :value="item.value"
              :key="item.value"
            >
              {{ item.name }}
            </option>
          </optgroup>
        </select>
      </div>
      <div class="group">
        <label for="speed">Sorting speed (in ms) </label>
        <input v-model="speed" id="speed" type="number" default="100" />
      </div>
      <div class="group">
        <label for="slider"> Array Size </label>
        <Slider id="slider" v-model="arraySize" :min="2" :max="100" />
      </div>
    </div>
    <!-- Sort Algorithm Visualizer -->
    <Bars :arraySize="arraySize" :speed="speed" :sortingAlgorithm="selectedAlgorithm" />
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
