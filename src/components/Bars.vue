<script setup>
import { ref, watchEffect } from 'vue'

const props = defineProps({
  speed: Number,
  arraySize: Number
})

let audioContext = null
const array = ref([])
const currentMove = ref(null)
const sorting = ref(false)

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
  node.gain.value = 0.1
  node.gain.linearRampToValueAtTime(0, audioContext.currentTime + duration)
  oscillator.connect(node)
  node.connect(audioContext.destination)
}

function boobleSort(arrayToSort) {
  let swapped
  const moves = []
  do {
    swapped = false
    for (let i = 1; i < arrayToSort.length; i++) {
      if (arrayToSort[i - 1] > arrayToSort[i]) {
        ;[arrayToSort[i - 1], arrayToSort[i]] = [arrayToSort[i], arrayToSort[i - 1]]
        moves.push({
          indices: [i - 1, i],
          swap: true
        })
        swapped = true
      }
    }
  } while (swapped)
  return moves
}

function sort() {
  // TO-DO dont sort if already sorting
  const moves = boobleSort([...array.value])
  sorting.value = true
  animate(moves)
}

function animate(moves) {
  if (moves.length === 0) {
    currentMove.value = null
    return
  }
  const move = moves.shift()
  currentMove.value = move.indices
  const [i, j] = move.indices
  ;[array.value[i], array.value[j]] = [array.value[j], array.value[i]]
  playNote(200 + array.value[i] * 500)
  playNote(200 + array.value[j] * 500)
  setTimeout(() => {
    animate(moves)
  }, props.speed)
}
</script>

<template>
  <div class="container">
    <div
      v-for="(item, index) in array"
      :key="index"
      :style="{
        height: `${item * 100}%`,
        backgroundColor: currentMove && currentMove.includes(index) ? 'red' : 'white'
      }"
      class="bar"
    ></div>
  </div>
  <div>
    <button @click.prevent="initArray">randomize</button>
    <button @click.prevent="sort">sort</button>
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
</style>
