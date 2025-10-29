<template>
  <div class="min-h-screen bg-gradient-to-br from-purple-900 via-blue-900 to-indigo-900 p-8">
    <div class="max-w-6xl mx-auto">
      <!-- Back Button -->
      <div class="mb-6">
        <button
          @click="goBack"
          class="bg-white/20 hover:bg-white/30 text-white px-6 py-3 rounded-lg backdrop-blur-lg border-2 border-white/30 transition-all duration-200 hover:scale-105 font-semibold flex items-center gap-2"
        >
          <span>←</span>
          <span>Back to Home</span>
        </button>
      </div>

      <!-- Header -->
      <div class="text-center mb-8">
        <h1 class="text-4xl md:text-5xl font-bold text-white mb-4">
          Health Dimensions Match
        </h1>
        <p class="text-xl text-blue-200">
          Drag each image to its matching health dimension box
        </p>
      </div>

      <!-- Game Complete Message -->
      <div v-if="isGameComplete" class="text-center mb-6">
        <div class="bg-green-500 text-white px-6 py-4 rounded-lg inline-block animate-bounce">
          <span class="text-2xl font-bold">🎉 Perfect! All matches are correct! 🎉</span>
        </div>
      </div>

      <!-- Game Container -->
      <div class="grid md:grid-cols-2 gap-8 mb-8">
        <!-- Draggable Images Section -->
        <div class="bg-white/10 backdrop-blur-lg rounded-2xl p-6 border-2 border-white/20">
          <h2 class="text-2xl font-bold text-white mb-4 text-center">Images</h2>
          <div class="grid grid-cols-2 gap-4">
            <div
              v-for="image in availableImages"
              :key="image.id"
              class="relative"
            >
              <img
                :src="image.src"
                :alt="image.alt"
                :draggable="!image.placed"
                @dragstart="handleDragStart($event, image)"
                @dragend="handleDragEnd"
                :class="[
                  'w-full h-48 object-cover rounded-lg border-4 transition-all duration-200',
                  image.placed 
                    ? 'opacity-30 cursor-not-allowed border-gray-500' 
                    : 'cursor-move border-white hover:border-yellow-400 hover:scale-105 hover:shadow-2xl',
                  isDragging && currentDragImage?.id === image.id ? 'opacity-50' : ''
                ]"
              />
              <div
                v-if="image.placed"
                class="absolute inset-0 flex items-center justify-center bg-black/50 rounded-lg"
              >
                <span class="text-white text-2xl font-bold">✓</span>
              </div>
            </div>
          </div>
        </div>

        <!-- Drop Zones Section -->
        <div class="bg-white/10 backdrop-blur-lg rounded-2xl p-6 border-2 border-white/20">
          <h2 class="text-2xl font-bold text-white mb-4 text-center">Health Dimensions</h2>
          <div class="grid grid-cols-1 gap-4">
            <div
              v-for="box in dropBoxes"
              :key="box.id"
              @dragover="handleDragOver($event, box)"
              @dragleave="handleDragLeave(box)"
              @drop="handleDrop($event, box)"
              :class="[
                'min-h-32 rounded-lg border-4 transition-all duration-300 flex flex-col items-center justify-center p-4',
                box.state === 'correct' 
                  ? 'bg-green-500 border-green-300 shadow-lg shadow-green-500/50' 
                  : box.state === 'error'
                  ? 'bg-red-500 border-red-300 animate-shake'
                  : box.state === 'hover'
                  ? 'bg-blue-500/30 border-blue-400 border-dashed scale-105'
                  : 'bg-white/5 border-white/40 border-dashed hover:border-white/60 hover:bg-white/10'
              ]"
            >
              <span class="text-2xl font-bold text-white mb-2">{{ box.label }}</span>
              <div v-if="box.placedImage" class="mt-2">
                <img
                  :src="box.placedImage.src"
                  :alt="box.placedImage.alt"
                  class="w-32 h-24 object-cover rounded-lg border-2 border-white"
                />
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Instructions -->
      <div class="bg-white/10 backdrop-blur-lg rounded-xl p-6 border-2 border-white/20">
        <h3 class="text-xl font-bold text-white mb-3">How to Play:</h3>
        <ul class="text-blue-200 space-y-2">
          <li>• <strong>Drag</strong> an image from the left panel to a matching box on the right</li>
          <li>• <strong>Green box</strong> = Correct match! ✓</li>
          <li>• <strong>Red flash</strong> = Wrong box, try again! ✗</li>
          <li>• Match all 4 health dimensions to complete the game</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

// Define emits
const emit = defineEmits(['goToGame'])

// Navigation
const goBack = () => {
  emit('goToGame')
}

// Define the health dimensions and their images
const images = ref([
  {
    id: 'physical',
    src: '/activity/physical-health.jpg',
    alt: 'Physical Health',
    correctBox: 'physical',
    placed: false
  },
  {
    id: 'mental',
    src: '/activity/mental-health.jpg',
    alt: 'Mental Health',
    correctBox: 'mental',
    placed: false
  },
  {
    id: 'spiritual',
    src: '/activity/spiritual-health.jpg',
    alt: 'Spiritual Health',
    correctBox: 'spiritual',
    placed: false
  },
  {
    id: 'social',
    src: '/activity/social-health.jpg',
    alt: 'Social Health',
    correctBox: 'social',
    placed: false
  }
])

// Define drop boxes
const dropBoxes = ref([
  {
    id: 'physical',
    label: 'Physical Health',
    state: 'default', // default, hover, correct, error
    placedImage: null
  },
  {
    id: 'mental',
    label: 'Mental Health',
    state: 'default',
    placedImage: null
  },
  {
    id: 'spiritual',
    label: 'Spiritual Health',
    state: 'default',
    placedImage: null
  },
  {
    id: 'social',
    label: 'Social Health',
    state: 'default',
    placedImage: null
  }
])

// Drag state
const isDragging = ref(false)
const currentDragImage = ref(null)

// Computed properties
const availableImages = computed(() => {
  return images.value
})

const isGameComplete = computed(() => {
  return dropBoxes.value.every(box => box.state === 'correct')
})

// Drag handlers
const handleDragStart = (event, image) => {
  if (image.placed) {
    event.preventDefault()
    return
  }
  
  isDragging.value = true
  currentDragImage.value = image
  event.dataTransfer.effectAllowed = 'move'
  event.dataTransfer.setData('imageId', image.id)
}

const handleDragEnd = () => {
  isDragging.value = false
  currentDragImage.value = null
}

const handleDragOver = (event, box) => {
  event.preventDefault()
  
  // Don't allow drop if box already has correct image
  if (box.state === 'correct') {
    event.dataTransfer.dropEffect = 'none'
    return
  }
  
  event.dataTransfer.dropEffect = 'move'
  
  // Highlight the box
  if (box.state !== 'correct' && box.state !== 'error') {
    box.state = 'hover'
  }
}

const handleDragLeave = (box) => {
  // Remove hover state if not correct or error
  if (box.state === 'hover') {
    box.state = 'default'
  }
}

const handleDrop = (event, box) => {
  event.preventDefault()
  
  // Don't allow drop if box already has correct image
  if (box.state === 'correct') {
    return
  }
  
  const imageId = event.dataTransfer.getData('imageId')
  const draggedImage = images.value.find(img => img.id === imageId)
  
  if (!draggedImage) return
  
  // Check if the image matches the box
  if (draggedImage.correctBox === box.id) {
    // Correct match!
    box.state = 'correct'
    box.placedImage = draggedImage
    draggedImage.placed = true
  } else {
    // Wrong match - show error
    box.state = 'error'
    
    // Reset after animation
    setTimeout(() => {
      box.state = 'default'
    }, 600)
  }
  
  isDragging.value = false
  currentDragImage.value = null
}
</script>

<style scoped>
@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-10px); }
  75% { transform: translateX(10px); }
}

.animate-shake {
  animation: shake 0.3s ease-in-out 2;
}
</style>

