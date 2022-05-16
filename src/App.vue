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

// const cardRefs = ref([])

let direction = 'up'
let prevYPosition = 0

const isElInViewport = (element) => {
  const rect = element.getBoundingClientRect();
  return (
      rect.top >= 0 &&
      rect.left >= 0 &&
      rect.bottom <= (window.innerHeight || document.documentElement.clientHeight) &&
      rect.right <= (window.innerWidth || document.documentElement.clientWidth)
  )
}

const setScrollDirection = () => {
  if (window.scrollY > prevYPosition) {
    direction = 'down'
  } else {
    direction = 'up'
  }

  prevYPosition = window.scrollY

  let focusIndex = 0

  if (direction === 'up') {
    // Bottom-most element if scrolling up
    focusIndex = isInViewport.value.lastIndexOf(true)

    if(!isElInViewport(document.querySelectorAll('.cards-text')[focusIndex])) {
      focusIndex = focusIndex - 1
    }
  } else if (direction === 'down') {
    // Top-most element if scrolling down
    focusIndex = isInViewport.value.indexOf(true)

    if(!isElInViewport(document.querySelectorAll('.cards-text')[focusIndex])) {
      focusIndex = focusIndex + 1
    }
  }

  // Reset array
  isInFocus.value.fill(false)

  isInFocus.value[focusIndex] = true
}

let onInitialLoadCalled = false

const onInitialLoad = () => {
  let focusIndex = focusIndex = isInViewport.value.indexOf(true)

  isInFocus.value[focusIndex] = true
}

onMounted(() => {
  const observer = new IntersectionObserver((entries) => {
    entries.forEach((entry) => {
      // Get index of entry
      const index = [...entry.target.parentElement.children].indexOf(entry.target)
      
      if (entry.isIntersecting) {
        isInViewport.value[index] = true
      } else {
        isInViewport.value[index] = false
      }
    })

    // Set focus to first value on initial load
    if(!onInitialLoadCalled) {
      onInitialLoad()

      onInitialLoadCalled = true
    }
  })

  document.querySelectorAll('.cards').forEach((card) => {
    observer.observe(card)
  })

  document.addEventListener('scroll', setScrollDirection)
})

onUnmounted(() => {
  document.removeEventListener('scroll', setScrollDirection)
})
</script>


<style>
.card-text {
  margin: 0px;
}
</style>
