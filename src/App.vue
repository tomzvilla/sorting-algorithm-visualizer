<script setup>
import { ref } from 'vue'
import Bars from './components/Bars.vue'
import Slider from '@vueform/slider'
import speakerOn from './assets/speaker-on.svg'
import speakerOff from './assets/speaker-off.svg'
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
const isMuted = ref(false)

function toggleMute() {
  isMuted.value = !isMuted.value
}
</script>

<template>
  <main>
    <div class="optionsContainer">
      <div class="header">
        <h1>Sort Algorithm Visualizer</h1>
        <button @click="toggleMute">
          <img :src="isMuted ? speakerOff : speakerOn" alt="" />
        </button>
      </div>
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
    <Bars
      :arraySize="arraySize"
      :speed="speed === '' ? 50 : speed"
      :sortingAlgorithm="selectedAlgorithm"
      :isMuted="isMuted"
    />
  </main>
</template>

<style scoped>
main {
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin: auto auto;
}

.header {
  margin-top: 2rem;
  display: flex;
  width: 100%;
  justify-content: space-evenly;
  align-items: center;
  color: #fff;
}

.header img {
  max-width: 36px;
  filter: invert(92%) sepia(100%) saturate(0%) hue-rotate(202deg) brightness(106%) contrast(106%);
}

.header > button {
  background-color: transparent;
  border-style: none;
  box-sizing: border-box;
  cursor: pointer;
}

.optionsContainer {
  display: flex;
  flex-direction: column;
  width: 35%;
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

h1 {
  text-align: center;
  font-size: 2.5rem;
}

@media screen and (max-width: 1024px) {
  .optionsContainer {
    width: 65%;
  }
  h1 {
    text-align: center;
    font-size: 2rem;
  }
}

@media screen and (max-width: 375px) {
  .optionsContainer {
    width: 85%;
  }
}
</style>
