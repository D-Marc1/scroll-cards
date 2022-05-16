<template>
    <div>
      <Card v-for="(card, id) in cards" class="cards" :cardText="card" :key="id" :is-in-focus="isInFocus[id]">
        <p class="cards-text">{{card}}</p>
      </Card>
    </div>
</template>

<script setup>
import Card from '@/components/Card.vue'
import { ref, onMounted, onUnmounted } from 'vue';

const cards = Array.from(Array(100), (_,x) => `Card ${x+1}`);

// Add code to this file to complete the task
const isInViewport = ref([...Array(100)].fill(false))
const isInFocus = ref([...Array(100)].fill(false))

let direction = 'up'
let prevYPosition = 0

let onInitialLoadCalled = false

const setScrollDirection = () => {
  if (window.scrollY > prevYPosition) {
    direction = 'down'
  } else {
    direction = 'up'
  }

  prevYPosition = window.scrollY
}

const isElInViewport = (element) => {
  const rect = element.getBoundingClientRect()

  // Add threshold so text appears off screen
  const threshold = 5

  return (
    rect.top >= -threshold && rect.bottom - threshold <= window.innerHeight
  )
}

const setCardTextInViewport = () => {
  document.querySelectorAll('.cards').forEach((card, index) => {
    const cardText = card.querySelector('.cards-text')

    // Checking whether fully visible
    if(isElInViewport(cardText)) {
      isInViewport.value[index] = true
    } else {
      isInViewport.value[index] = false
    }
  })
}

const onInitialLoad = () => {
  setCardTextInViewport()

  let focusIndex = focusIndex = isInViewport.value.indexOf(true)

  isInFocus.value[focusIndex] = true
}

const onScroll = () => {
  setScrollDirection()

  setCardTextInViewport()

  let focusIndex = 0

  if (direction === 'up') {
    // Bottom-most element if scrolling up
    focusIndex = isInViewport.value.lastIndexOf(true)
  } else if (direction === 'down') {
    // Top-most element if scrolling down
    focusIndex = isInViewport.value.indexOf(true)
  }

  // Reset array
  isInFocus.value.fill(false)

  isInFocus.value[focusIndex] = true
}

onMounted(() => {
  // Set focus to first value on initial load
  if(!onInitialLoadCalled) {
    onInitialLoad()

    onInitialLoadCalled = true
  }

  document.addEventListener('scroll', onScroll)
})

onUnmounted(() => {
  document.removeEventListener('scroll', onScroll)
})
</script>


<style>
.cards-text {
  margin: 0px;
}
</style>
