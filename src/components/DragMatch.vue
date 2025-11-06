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
          Drag the correct health dimension box to match the description
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
        <!-- Description Section (Left Side) -->
        <div 
          data-drop-zone="true"
          @dragover="handleDragOver($event)"
          @dragleave="handleDragLeave"
          @drop="handleDrop($event)"
          class="bg-white/10 backdrop-blur-lg rounded-2xl p-8 border-4 border-white/40 min-h-96 flex flex-col justify-center items-center transition-all duration-300"
                :class="[
            dropZoneState === 'hover' ? 'bg-blue-500/30 border-blue-400 border-dashed scale-105' : '',
            dropZoneState === 'correct' ? 'bg-green-500/30 border-green-400' : '',
            dropZoneState === 'error' ? 'bg-red-500/30 border-red-400 animate-shake' : ''
          ]"
        >
          <div class="text-center">
            <div class="mb-6">
              <span class="text-white/70 text-lg">Question {{ currentDescriptionIndex + 1 }} of {{ descriptions.length }}</span>
            </div>
            <div v-if="currentDescription">
              <p class="text-2xl md:text-3xl font-semibold text-white leading-relaxed">
                {{ currentDescription.text }}
              </p>
              </div>
            <div v-else class="text-white text-xl">
              All questions completed!
            </div>
          </div>
        </div>

        <!-- Draggable Boxes Section (Right Side) -->
        <div class="bg-white/10 backdrop-blur-lg rounded-2xl p-6 border-2 border-white/20">
          <h2 class="text-2xl font-bold text-white mb-4 text-center">Health Dimensions</h2>
          <div class="grid grid-cols-1 gap-4">
            <div
              v-for="box in draggableBoxes"
              :key="box.id"
              :draggable="!box.used"
              @dragstart="handleDragStart($event, box)"
              @dragend="handleDragEnd"
              @touchstart="handleTouchStart($event, box)"
              @touchmove="handleTouchMove($event)"
              @touchend="handleTouchEnd($event, box)"
              :class="[
                'rounded-lg border-4 transition-all duration-200 p-6 cursor-move',
                box.used 
                  ? 'opacity-30 cursor-not-allowed border-gray-500 bg-gray-700/50' 
                  : 'border-white bg-white/5 hover:border-yellow-400 hover:scale-105 hover:shadow-2xl hover:bg-white/10',
                isDragging && currentDragBox?.id === box.id ? 'opacity-50' : ''
              ]"
            >
              <div class="flex items-center justify-between">
                <span class="text-2xl font-bold text-white">{{ box.label }}</span>
                <span v-if="box.used" class="text-green-400 text-3xl">✓</span>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Instructions -->
      <div class="bg-white/10 backdrop-blur-lg rounded-xl p-6 border-2 border-white/20">
        <h3 class="text-xl font-bold text-white mb-3">How to Play:</h3>
        <ul class="text-blue-200 space-y-2">
          <li>• <strong>Read</strong> the description on the left</li>
          <li>• <strong>Drag</strong> the matching health dimension box to the description</li>
          <li>• <strong>Green highlight</strong> = Correct match! Move to next question</li>
          <li>• <strong>Red flash</strong> = Wrong box, try again!</li>
          <li>• Match all 4 descriptions to complete the game</li>
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

// Define descriptions
const descriptions = ref([
  {
    id: 1,
    text: "Lloyd makes sure he eats fruits and vegetables every day and drinks plenty of water.",
    correctBox: 'physical',
    matched: false
  },
  {
    id: 2,
    text: "A group of friends volunteers to clean their community park during the weekend.",
    correctBox: 'social',
    matched: false
  },
  
  {
    id: 4,
    text: "John feels stressed about his upcoming exams, so he takes a short break to meditate and clear his mind.",
    correctBox: 'mental',
    matched: false
  }
])

// Define draggable boxes
const draggableBoxes = ref([
  {
    id: 'physical',
    label: 'Physical Health',
    used: false
  },
  {
    id: 'mental',
    label: 'Mental Health',
    used: false
  },
  
  {
    id: 'social',
    label: 'Social Health',
    used: false
  }
])

// Game state
const currentDescriptionIndex = ref(0)
const dropZoneState = ref('default') // default, hover, correct, error

// Drag state
const isDragging = ref(false)
const currentDragBox = ref(null)
const touchStartPos = ref({ x: 0, y: 0 })
const isTouchDragging = ref(false)

// Computed properties
const currentDescription = computed(() => {
  return descriptions.value[currentDescriptionIndex.value]
})

const isGameComplete = computed(() => {
  return descriptions.value.every(desc => desc.matched)
})

// Drag handlers
const handleDragStart = (event, box) => {
  if (box.used || !currentDescription.value) {
    event.preventDefault()
    return
  }
  
  isDragging.value = true
  currentDragBox.value = box
  event.dataTransfer.effectAllowed = 'move'
  event.dataTransfer.setData('boxId', box.id)
}

const handleDragEnd = () => {
  isDragging.value = false
  currentDragBox.value = null
  // Reset drop zone state if not in correct or error state
  if (dropZoneState.value === 'hover') {
    dropZoneState.value = 'default'
  }
}

const handleDragOver = (event) => {
  if (!currentDescription.value) return
  
  event.preventDefault()
  event.dataTransfer.dropEffect = 'move'
  
  // Highlight the drop zone
  if (dropZoneState.value !== 'correct' && dropZoneState.value !== 'error') {
    dropZoneState.value = 'hover'
  }
}

const handleDragLeave = () => {
  // Remove hover state if not correct or error
  if (dropZoneState.value === 'hover') {
    dropZoneState.value = 'default'
  }
}

const handleDrop = (event) => {
  event.preventDefault()
  
  if (!currentDescription.value) return
  
  const boxId = event.dataTransfer.getData('boxId')
  const draggedBox = draggableBoxes.value.find(box => box.id === boxId)
  
  if (!draggedBox || draggedBox.used) return
  
  processDrop(draggedBox)
  
  isDragging.value = false
  currentDragBox.value = null
}

// Touch handlers for mobile
const touchPosition = ref({ x: 0, y: 0 })
const currentTouchElement = ref(null)

const handleTouchStart = (event, box) => {
  if (box.used || !currentDescription.value) {
    console.log('Touch start blocked: box used or no description')
    return
  }
  
  console.log('Touch start on box:', box.label)
  const touch = event.touches[0]
  touchStartPos.value = { x: touch.clientX, y: touch.clientY }
  touchPosition.value = { x: touch.clientX, y: touch.clientY }
  isTouchDragging.value = true
  currentDragBox.value = box
  isDragging.value = true
  currentTouchElement.value = event.currentTarget
}

const handleTouchMove = (event) => {
  if (!isTouchDragging.value) return
  
  const touch = event.touches[0]
  touchPosition.value = { x: touch.clientX, y: touch.clientY }
  
  console.log('Touch move at:', touch.clientX, touch.clientY)
  
  // Check if we're over the drop zone
  const elementAtPoint = document.elementFromPoint(touch.clientX, touch.clientY)
  
  if (elementAtPoint) {
    const dropZone = elementAtPoint.closest('[data-drop-zone]')
    if (dropZone && currentDescription.value) {
      console.log('Over drop zone')
      // Highlight the drop zone
      if (dropZoneState.value !== 'correct' && dropZoneState.value !== 'error') {
        dropZoneState.value = 'hover'
      }
    } else if (dropZoneState.value === 'hover') {
      dropZoneState.value = 'default'
    }
  }
}

const handleTouchEnd = (event, box) => {
  console.log('Touch end called, isTouchDragging:', isTouchDragging.value)
  
  if (!isTouchDragging.value || !currentDragBox.value || !currentDescription.value) {
    console.log('Touch end blocked: not dragging or missing data')
    isTouchDragging.value = false
    isDragging.value = false
    currentDragBox.value = null
    if (dropZoneState.value === 'hover') {
      dropZoneState.value = 'default'
    }
    return
  }
  
  const touch = event.changedTouches[0]
  const elementAtPoint = document.elementFromPoint(touch.clientX, touch.clientY)
  
  console.log('Touch end at element:', elementAtPoint?.tagName)
  
  // Check if the touch ended over the drop zone
  if (elementAtPoint) {
    const dropZone = elementAtPoint.closest('[data-drop-zone]')
    if (dropZone && currentDragBox.value) {
      console.log('Dropping box on description:', currentDragBox.value.label)
      processDrop(currentDragBox.value)
        } else {
      console.log('Not dropped on description zone')
      // Didn't drop on the description zone - reset
      if (dropZoneState.value === 'hover') {
        dropZoneState.value = 'default'
      }
    }
  } else {
    if (dropZoneState.value === 'hover') {
      dropZoneState.value = 'default'
    }
  }
  
  isTouchDragging.value = false
  isDragging.value = false
  currentDragBox.value = null
  currentTouchElement.value = null
}


// Helper function to process drops (used by both desktop and mobile)
const processDrop = (draggedBox) => {
  if (!draggedBox || draggedBox.used || !currentDescription.value) return
  
  // Check if the box matches the current description
  if (draggedBox.id === currentDescription.value.correctBox) {
    // Correct match!
    dropZoneState.value = 'correct'
    currentDescription.value.matched = true
    draggedBox.used = true
    
    // Move to next description after a brief delay
    setTimeout(() => {
      if (currentDescriptionIndex.value < descriptions.value.length - 1) {
        currentDescriptionIndex.value++
        dropZoneState.value = 'default'
      }
    }, 1000)
  } else {
    // Wrong match - show error
    dropZoneState.value = 'error'
    
    // Reset after animation
    setTimeout(() => {
      dropZoneState.value = 'default'
    }, 600)
  }
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

/* Desktop: allow drag */
@media (hover: hover) and (pointer: fine) {
  [draggable="true"] {
    cursor: move;
    touch-action: auto;
  }
}

/* Mobile: disable default touch behaviors but enable custom touch */
@media (hover: none) and (pointer: coarse) {
  [draggable="true"] {
    cursor: pointer;
    touch-action: none;
  }
}

/* Allow touch on drop zones */
div[data-drop-zone] {
  touch-action: auto;
}
</style>