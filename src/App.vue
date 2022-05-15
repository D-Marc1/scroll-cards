<template>
    <div>
      <Card v-for="(card, id) in cards" class="cards" :cardText="card" :key="id" :is-in-focus="isInFocus[id]">
        <p>{{card}}</p>
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

const setScrollDirection = () => {
  if (window.scrollY > prevYPosition) {
    direction = 'down'
  } else {
    direction = 'up'
  }

  prevYPosition = window.scrollY

  let focusIndex = 0

  if (direction === 'up') {
    focusIndex = isInViewport.value.lastIndexOf(true)
  } else if (direction === 'down') {
    focusIndex = isInViewport.value.indexOf(true)
  }

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
      const index = [...entry.target.parentElement.children].indexOf(entry.target)
      
      if (entry.isIntersecting) {
        isInViewport.value[index] = true

        console.log(entry)
      } else {
        isInViewport.value[index] = false
      }

      if(!onInitialLoadCalled) {
        onInitialLoad()

        onInitialLoadCalled = true
      }
    })
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
</style>
