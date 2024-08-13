<script setup>
import { ref, watchEffect } from 'vue'
const props = defineProps({
  speed: Number,
  arraySize: Number,
  sortingAlgorithm: String,
  isMuted: Boolean
})

let audioContext = null
const array = ref([])
const currentMove = ref(null)
const moves = ref([])
const isSorting = ref(false)

function randomize() {
  if (isSorting.value) return
  initArray()
}

function initArray() {
  array.value = []
  for (let i = 0; i < props.arraySize; i++) {
    array.value.push(Math.random())
  }
  currentMove.value = null
}

watchEffect(() => {
  initArray()
})

function playNote(freq) {
  if (audioContext === null) {
    audioContext = new (AudioContext || webkitAudioContext || window.webkitAudioContext)()
  }
  const duration = 0.1
  const oscillator = audioContext.createOscillator()
  oscillator.frequency.value = freq
  oscillator.start()
  oscillator.stop(audioContext.currentTime + duration)
  const node = audioContext.createGain()
  node.gain.value = props.isMuted ? 0 : 0.1
  node.gain.linearRampToValueAtTime(0, audioContext.currentTime + duration)
  oscillator.connect(node)
  node.connect(audioContext.destination)
}

/* SELECTION SORT */

function selectionSort(arrayToSort) {
  for (let i = 0; i < arrayToSort.length; i++) {
    let min = i
    for (let j = i + 1; j < arrayToSort.length; j++) {
      if (arrayToSort[j] < arrayToSort[min]) {
        min = j
      }
    }
    if (min !== i) {
      ;[arrayToSort[i], arrayToSort[min]] = [arrayToSort[min], arrayToSort[i]]
      moves.value.push({
        indices: [i, min],
        swap: true
      })
    }
  }
}

/* ////////// */

/* INSERTION SORT */

function insertionSort(arrayToSort) {
  for (let i = 1; i < arrayToSort.length; i++) {
    let current = arrayToSort[i]
    let j = i - 1
    while (j >= 0 && arrayToSort[j] > current) {
      arrayToSort[j + 1] = arrayToSort[j]
      moves.value.push({
        indices: [j + 1, j],
        swap: true,
        value: arrayToSort[j]
      })
      j--
    }
    arrayToSort[j + 1] = current
    moves.value.push({
      indices: [j + 1, i],
      swap: false,
      value: current
    })
  }
}

/* ////////// */

/* BUBBLE SORT */

function bubbleSort(arrayToSort) {
  let swapped
  do {
    swapped = false
    for (let i = 1; i < arrayToSort.length; i++) {
      if (arrayToSort[i - 1] > arrayToSort[i]) {
        ;[arrayToSort[i - 1], arrayToSort[i]] = [arrayToSort[i], arrayToSort[i - 1]]
        moves.value.push({
          indices: [i - 1, i],
          swap: true
        })
        swapped = true
      }
    }
  } while (swapped)
}

/* ////////// */

/* QUICK SORT */

function partition(arr, left, right) {
  let pivotValue = arr[right]
  let i = left - 1
  for (let j = left; j < right; j++) {
    if (arr[j] < pivotValue) {
      i++
      moves.value.push({
        indices: [i, j],
        swap: true
      })
      ;[arr[i], arr[j]] = [arr[j], arr[i]]
    }
  }
  moves.value.push({
    indices: [i + 1, right],
    swap: true
  })
  ;[arr[i + 1], arr[right]] = [arr[right], arr[i + 1]]
  return i + 1
}

function quickSort(arr, left = 0, right = arr.length - 1) {
  if (left < right) {
    let pivotIndex = partition(arr, left, right)
    quickSort(arr, left, pivotIndex - 1)
    quickSort(arr, pivotIndex + 1, right)
  }
}

/* ////////// */

/* HEAP SORT */

function heapSort(arr) {
  for (let i = Math.floor(arr.length / 2) - 1; i >= 0; i--) {
    heapify(arr, arr.length, i)
  }

  for (let i = arr.length - 1; i > 0; i--) {
    ;[arr[0], arr[i]] = [arr[i], arr[0]]
    moves.value.push({
      indices: [0, i],
      swap: true
    })
    heapify(arr, i, 0)
  }
}

function heapify(arr, N, i) {
  let largest = i
  let left = 2 * i + 1
  let right = 2 * i + 2

  if (left < N && arr[left] > arr[largest]) {
    largest = left
  }
  if (right < N && arr[right] > arr[largest]) {
    largest = right
  }
  if (largest != i) {
    moves.value.push({
      indices: [i, largest],
      swap: true
    })
    ;[arr[i], arr[largest]] = [arr[largest], arr[i]]
    heapify(arr, N, largest)
  }
}

/* ////////// */

/* MERGE SORT */

function mergeSort(arrayToSort, left = 0, right = arrayToSort.length - 1) {
  if (left < right) {
    const mid = Math.floor((left + right) / 2)

    mergeSort(arrayToSort, left, mid)
    mergeSort(arrayToSort, mid + 1, right)

    merge(arrayToSort, left, mid, right)
  }
}

function merge(arrayToSort, left, mid, right) {
  const n1 = mid - left + 1
  const n2 = right - mid

  const leftArray = arrayToSort.slice(left, mid + 1)
  const rightArray = arrayToSort.slice(mid + 1, right + 1)

  let i = 0,
    j = 0,
    k = left

  while (i < n1 && j < n2) {
    if (leftArray[i] <= rightArray[j]) {
      moves.value.push({ indices: [k, k + i - left], value: leftArray[i] })
      arrayToSort[k] = leftArray[i]
      i++
    } else {
      moves.value.push({ indices: [k, k + j - mid - 1], value: rightArray[j] })
      arrayToSort[k] = rightArray[j]
      j++
    }
    k++
  }

  while (i < n1) {
    moves.value.push({ indices: [k, k + i - left], value: leftArray[i] })
    arrayToSort[k] = leftArray[i]
    i++
    k++
  }

  while (j < n2) {
    moves.value.push({ indices: [k, k + j - mid - 1], value: rightArray[j] })
    arrayToSort[k] = rightArray[j]
    j++
    k++
  }
}

/* ////////// */

function getSortingAlgorithm(type) {
  switch (type) {
    case 'bubbleSort':
      return bubbleSort
    case 'insertionSort':
      return insertionSort
    case 'selectionSort':
      return selectionSort
    case 'quickSort':
      return quickSort
    case 'heapSort':
      return heapSort
    case 'mergeSort':
      return mergeSort
    default:
      return bubbleSort
  }
}

async function sort() {
  if (isSorting.value) return
  isSorting.value = true
  moves.value = []
  const sortAlgorithm = getSortingAlgorithm(props.sortingAlgorithm)
  sortAlgorithm([...array.value])
  await animate(moves)
}

async function animate(moves) {
  return new Promise((resolve) => {
    if (moves.length === 0) {
      currentMove.value = null
      resolve()
      return
    }
    const move = moves.value.shift()
    if (!move) {
      currentMove.value = null
      resolve()
      return
    }
    const [i, j] = move?.indices
    const value = move?.value

    // Si hay un valor especificado en el movimiento, es un movimiento de fusión
    if (value !== undefined) {
      array.value[i] = value
      currentMove.value = [i]
    } else {
      // Si no hay valor, es un intercambio de bubble sort
      ;[array.value[i], array.value[j]] = [array.value[j], array.value[i]]
      currentMove.value = [i, j]
    }

    playNote(200 + array.value[i] * 500)
    if (j !== undefined && array.value[j] !== undefined) {
      playNote(200 + array.value[j] * 500)
    }
    setTimeout(() => {
      animate(moves).then(resolve)
    }, props.speed)
  }).then(() => animateSortedArray())
}

const animateSortedArray = () => {
  let index = 0
  const highlightNextBar = () => {
    if (index >= array.value.length) {
      playNote(900)
      setTimeout(() => {
        array.value.forEach((_, i) => {
          document.getElementById(`bar-${i}`).style.backgroundColor = 'white'
        })
      }, 250)
      isSorting.value = false
      return
    }
    document.getElementById(`bar-${index}`).style.backgroundColor = 'green'
    playNote(200 + array.value[index] * 500)
    setTimeout(() => {
      index++
      highlightNextBar()
    }, 50)
  }
  highlightNextBar()
}
</script>

<template>
  <div class="container" id="container">
    <div
      v-for="(item, index) in array"
      :key="index"
      :style="{
        height: `${item * 100}%`,
        backgroundColor: currentMove && currentMove.includes(index) ? 'red' : 'white'
      }"
      class="bar"
      :id="`bar-${index}`"
    ></div>
  </div>
  <div class="buttonContainer">
    <button class="btn" @click.prevent="randomize">Randomize</button>
    <button class="btn" @click.prevent="sort">Sort</button>
  </div>
</template>

<style lang="css">
.bar {
  background-color: white;
  width: 20px;
  margin-left: 2px;
}

.container {
  display: flex;
  align-items: flex-end;
  justify-content: center;
  height: 300px;
  max-width: 720px;
}

.buttonContainer {
  margin: 2rem;
  width: 100%;
  display: flex;
  justify-content: space-evenly;
  align-items: center;
}

.btn {
  background-color: #fff;
  border-radius: 4px;
  border-style: none;
  box-sizing: border-box;
  color: #222;
  cursor: pointer;
  display: inline-block;
  font-family: 'Farfetch Basis', 'Helvetica Neue', Arial, sans-serif;
  font-size: 16px;
  font-weight: 700;
  line-height: 1.5;
  margin: 0;
  max-width: none;
  min-height: 44px;
  min-width: 10px;
  padding: 9px 20px 8px;
  position: relative;
  text-align: center;
}

.green {
  background-color: green;
}

button:hover {
  opacity: 0.75;
}
</style>
