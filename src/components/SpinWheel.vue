<template>
  <div class="spin-wheel-page">
    <!-- Back button -->
    <button class="back-button" @click="$emit('goToGame')">
      ← Back to HOME
    </button>

    <!-- Header -->
    <header class="header">
      <h1>🎡 SPIN THE WHEEL 🎡</h1>
      <p class="subtitle">Spin the wheel to pick a random winner!</p>
    </header>

    <!-- Wheel Display -->
    <div class="wheel-display-container">
      <!-- Left Side: Wheel -->
      <div class="wheel-section">
        <div class="wheel-container" :class="{ spinning: isSpinning }">
          <svg class="wheel" viewBox="0 0 550 550" preserveAspectRatio="xMidYMid meet" :style="{ transform: `rotate(${rotationAngle}deg)` }">
            <!-- Gradient definitions -->
            <defs>
              <g v-for="(name, index) in names" :key="`gradient-${index}`">
                <linearGradient :id="`gradient-${index}`" x1="0%" y1="0%" x2="100%" y2="100%">
                  <stop offset="0%" :style="`stop-color:${getColor(index)};stop-opacity:1`" />
                  <stop offset="100%" :style="`stop-color:${getDarkColor(index)};stop-opacity:1`" />
                </linearGradient>
              </g>
              <!-- Outer glow for the wheel -->
              <filter id="glow">
                <feGaussianBlur stdDeviation="4" result="coloredBlur"/>
                <feMerge>
                  <feMergeNode in="coloredBlur"/>
                  <feMergeNode in="SourceGraphic"/>
                </feMerge>
              </filter>
              <!-- Shadow for slices -->
              <filter id="sliceShadow">
                <feGaussianBlur in="SourceAlpha" stdDeviation="2"/>
                <feOffset dx="0" dy="2" result="offsetblur"/>
                <feComponentTransfer>
                  <feFuncA type="linear" slope="0.3"/>
                </feComponentTransfer>
                <feMerge>
                  <feMergeNode/>
                  <feMergeNode in="SourceGraphic"/>
                </feMerge>
              </filter>
              <!-- Center gradient -->
              <linearGradient id="centerGradient" x1="0%" y1="0%" x2="100%" y2="100%">
                <stop offset="0%" style="stop-color:#667eea;stop-opacity:1" />
                <stop offset="100%" style="stop-color:#764ba2;stop-opacity:1" />
              </linearGradient>
            </defs>
            
            <!-- Wheel slices -->
            <g v-for="(name, index) in names" :key="index">
              <path
                :d="getSlicePath(index)"
                :fill="`url(#gradient-${index})`"
                class="slice"
                filter="url(#sliceShadow)"
              />
              <text
                :x="getTextX(index)"
                :y="getTextY(index)"
                :transform="getTextTransform(index)"
                :style="{ fontSize: getTextSize() + 'px' }"
                class="slice-text"
              >
                {{ name }}
              </text>
            </g>
            
            <!-- Center circle with gradient -->
            <circle cx="275" cy="275" r="75" fill="#fff" stroke="url(#centerGradient)" stroke-width="6" filter="url(#glow)" opacity="0.95"/>
            <circle cx="275" cy="275" r="65" fill="#fff"/>
            <circle cx="275" cy="275" r="30" fill="url(#centerGradient)" class="center-dot"/>
            <circle cx="275" cy="275" r="18" fill="#fff" opacity="0.9"/>
          </svg>
          <!-- Pointer with glow effect -->
          <div class="pointer">
            <div class="pointer-glow"></div>
          </div>
          
          <!-- Spin Button in Center -->
          <button @click="spinWheel" class="spin-button-center" :disabled="isSpinning">
            <span v-if="isSpinning" class="spin-center-text">
              <span class="spinner-icon">⏳</span>
              <span class="spinner-label">SPINNING...</span>
            </span>
            <span v-else class="spin-center-text">
              <span class="spinner-icon">🎡</span>
              <span class="spinner-label">SPIN!</span>
            </span>
          </button>
        </div>
      </div>

      <!-- Right Side: Result -->
      <div class="result-section">
        <div v-if="winner" class="winner-display-full">
          <div class="winner-icon">🎉</div>
          <h2 class="winner-title">The Winner Is...</h2>
          <p class="winner-name">{{ winner }}</p>
          <button @click="resetSelection" class="reset-button-alt">
            🔄 Reset & Start Over
          </button>
        </div>
        <div v-else class="waiting-display">
          <div class="waiting-icon">🎡</div>
          <h2 class="waiting-title">Ready to Spin?</h2>
          <p class="waiting-text">Click the spin button to pick a random winner!</p>
          <div class="selection-stats">
            <div class="stat-item">
              <span class="stat-label">Available:</span>
              <span class="stat-value available">{{ names.length - selectedNames.length }}</span>
            </div>
            <div class="stat-item">
              <span class="stat-label">Selected:</span>
              <span class="stat-value selected">{{ selectedNames.length }}</span>
            </div>
          </div>
          <button v-if="selectedNames.length > 0" @click="resetSelection" class="reset-button">
            Reset Selection
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

const emit = defineEmits(['goToGame'])

// Force re-render on window resize for responsive text sizing
const windowWidth = ref(window.innerWidth)

const handleResize = () => {
  windowWidth.value = window.innerWidth
}

onMounted(() => {
  window.addEventListener('resize', handleResize)
})

onUnmounted(() => {
  window.removeEventListener('resize', handleResize)
})

const names = ref([
  'samo',
  'sabang',
  'porras',
  'Francisco',
  'marcelo',
  'frusa',
  'afinidad',
  'lusaya',
  'pendatun',
  'labto',
  'parahili',
  'viesca',
  'kamansa',
  'pañara',
  'peñaflorida',
  'lebrillio',
  'pama',
  'lumawig',
  'Gonzales',
  'susing',
  'baylosis',
  'tiwan',
  'lamputi',
  'sudaw',
  'baradan',
  'estorque',
  'Guadalupe',
  'talili',
  'turla',
  'elegido',
  'tapulado',
  'pagaringan',
  'balanag',
  'calumba',
  'lamita'
])
const selectedNames = ref([])
const isSpinning = ref(false)
const winner = ref('')
const rotationAngle = ref(0)
const wheelSize = 550

// Vibrant color palette for the wheel
const colors = [
  '#FF6B6B', '#4ECDC4', '#45B7D1', '#FFA07A', '#98D8C8',
  '#F7DC6F', '#BB8FCE', '#85C1E2', '#F8B500', '#6C5CE7',
  '#FD79A8', '#00B894', '#0984E3', '#E17055', '#A29BFE',
  '#00D2D3', '#FF9F43', '#5F27CD', '#00D8FF', '#FFA5A5',
  '#00FF88', '#FFC312', '#EA2027', '#1289A7', '#9980FA',
  '#48C9B0', '#F368E0', '#FF9FF3', '#54A0FF', '#FF6348',
  '#2ED573', '#FFA502', '#9980FA', '#FC427B', '#55E6C1',
  '#CAD3C8', '#6A5ACD', '#FFB142', '#70A1D7', '#81ECEC'
]

const darkerColors = [
  '#E55555', '#3DB3A3', '#3590B8', '#E89260', '#7FB8A3',
  '#E0C75F', '#A975D2', '#6AA1C2', '#E09400', '#5845C7',
  '#E56192', '#009877', '#0872C3', '#C55A3F', '#8E8ADE',
  '#00BDBD', '#E88F2F', '#5324A1', '#00C2E8', '#E89595',
  '#00E079', '#E8AF0A', '#D21618', '#0F7597', '#8B70D4',
  '#36B398', '#E350C3', '#E88FC7', '#4A7FDD', '#E55235',
  '#26C565', '#E89500', '#8B70D4', '#E13D6B', '#45CEB1',
  '#B5C3B0', '#5849B5', '#E89F2E', '#5890B7', '#66D6D6'
]

const getColor = (index) => {
  return colors[index % colors.length]
}

const getDarkColor = (index) => {
  return darkerColors[index % darkerColors.length]
}

const getSlicePath = (index) => {
  if (names.value.length === 0) return ''
  
  const numSlices = names.value.length
  const sliceAngle = (2 * Math.PI) / numSlices
  
  // Calculate angles precisely - rotate so top is at -90 degrees
  const startAngle = index * sliceAngle - Math.PI / 2
  const endAngle = (index + 1) * sliceAngle - Math.PI / 2
  
  const centerX = 275
  const centerY = 275
  const radius = 275
  
  // Calculate points without rounding to maintain precision
  const x1 = centerX + radius * Math.cos(startAngle)
  const y1 = centerY + radius * Math.sin(startAngle)
  const x2 = centerX + radius * Math.cos(endAngle)
  const y2 = centerY + radius * Math.sin(endAngle)
  
  // Determine if we need large arc (for angles > 180 degrees)
  const largeArc = sliceAngle > Math.PI ? 1 : 0
  
  // Build the path: Move to center, line to start, arc to end, close
  return `M ${centerX} ${centerY} L ${x1} ${y1} A ${radius} ${radius} 0 ${largeArc} 1 ${x2} ${y2} Z`
}

const getTextRadius = () => {
  const numSlices = names.value.length
  const isMobile = windowWidth.value <= 768
  const isSmallMobile = windowWidth.value <= 480
  
  // Adjust radius based on screen size for better text positioning
  const baseRadius = isSmallMobile ? 0.55 : isMobile ? 0.60 : 0.65
  
  if (numSlices <= 8) return 180 * baseRadius / 0.65
  if (numSlices <= 15) return 165 * baseRadius / 0.65
  if (numSlices <= 25) return 150 * baseRadius / 0.65
  return 135 * baseRadius / 0.65
}

const getTextX = (index) => {
  if (names.value.length === 0) return 275
  const numSlices = names.value.length
  const radius = getTextRadius()
  const angle = (index * 2 * Math.PI / numSlices) + (Math.PI / numSlices) - Math.PI / 2
  return 275 + radius * Math.cos(angle)
}

const getTextY = (index) => {
  if (names.value.length === 0) return 275
  const numSlices = names.value.length
  const radius = getTextRadius()
  const angle = (index * 2 * Math.PI / numSlices) + (Math.PI / numSlices) - Math.PI / 2
  return 275 + radius * Math.sin(angle)
}

const getTextTransform = (index) => {
  if (names.value.length === 0) return ''
  const numSlices = names.value.length
  // Rotate text to align with the middle of each slice
  const rotationAngle = (index * 360 / numSlices) + (180 / numSlices) - 90
  const x = getTextX(index)
  const y = getTextY(index)
  return `rotate(${rotationAngle}, ${x}, ${y})`
}

const getTextSize = () => {
  const numSlices = names.value.length
  const isMobile = windowWidth.value <= 768
  const isSmallMobile = windowWidth.value <= 480
  
  if (isSmallMobile) {
    if (numSlices <= 8) return 10
    if (numSlices <= 15) return 9
    if (numSlices <= 25) return 8
    return 7
  }
  
  if (isMobile) {
    if (numSlices <= 8) return 12
    if (numSlices <= 15) return 11
    if (numSlices <= 25) return 10
    return 8
  }
  
  if (numSlices <= 8) return 16
  if (numSlices <= 15) return 14
  if (numSlices <= 25) return 12
  return 10
}

const spinWheel = () => {
  // Get available names (not yet selected)
  const availableNames = names.value.filter(name => !selectedNames.value.includes(name))
  
  // Check if all names are selected
  if (availableNames.length === 0) {
    if (confirm('All names have been selected! Do you want to reset and start over?')) {
      resetSelection()
    }
    return
  }
  
  // Clear winner immediately
  winner.value = ''
  isSpinning.value = true
  
  // Small delay to ensure UI updates before starting spin
  setTimeout(() => {
    // Pick a random winner from available names
    const randomIndex = Math.floor(Math.random() * availableNames.length)
    const selectedName = availableNames[randomIndex]
    
    // Find the index of the selected name in the original names array
    const winnerIndex = names.value.indexOf(selectedName)
    
    // Calculate the exact rotation to land on the winner
    const slices = names.value.length
    const sliceAngleDegrees = 360 / slices
    
    // Slices are drawn starting from top (-90° or 270° in standard coordinates)
    // After CSS rotation R, the wheel has rotated R degrees clockwise
    // So what was originally at angle A is now at angle (A + R) mod 360
    
    // The winning slice center (from original position):
    // Each slice is sliceAngleDegrees wide
    // Slice center is at: sliceIndex * sliceAngleDegrees + sliceAngleDegrees/2
    const originalSliceCenterAngle = winnerIndex * sliceAngleDegrees + (sliceAngleDegrees / 2)
    
    // After current rotation R, this center is at:
    const currentRotation = rotationAngle.value % 360
    const currentCenterAngle = (originalSliceCenterAngle + currentRotation) % 360
    
    // To align the center with pointer at top (0°), we need to rotate by:
    // After rotating by delta, the center will be at: (currentCenterAngle + delta) mod 360
    // We want this to equal 0, so: delta = -currentCenterAngle mod 360 = (360 - currentCenterAngle) mod 360
    let delta = (360 - currentCenterAngle) % 360
    if (delta === 0) delta = 360 // Ensure at least some rotation
    
    // Add full rotations for visual spinning effect  
    const fullRotations = 5
    const totalAdditionalRotation = fullRotations * 360 + delta
    
    // Apply the total rotation
    rotationAngle.value = rotationAngle.value + totalAdditionalRotation
    
    // Simulate spinning duration
    setTimeout(() => {
      winner.value = selectedName
      // Add to selected names
      selectedNames.value.push(selectedName)
      isSpinning.value = false
    }, 3000)
  }, 50)
}

const resetSelection = () => {
  selectedNames.value = []
  winner.value = ''
  rotationAngle.value = 0
}
</script>

<style scoped>
.spin-wheel-page {
  min-height: 100vh;
  padding: 40px 20px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  display: flex;
  flex-direction: column;
  align-items: center;
  position: relative;
}

.back-button {
  position: absolute;
  top: 20px;
  left: 20px;
  padding: 12px 24px;
  background: rgba(255, 255, 255, 0.2);
  backdrop-filter: blur(10px);
  border: 2px solid rgba(255, 255, 255, 0.3);
  color: white;
  border-radius: 25px;
  cursor: pointer;
  font-size: 16px;
  font-weight: 600;
  transition: all 0.3s ease;
  z-index: 100;
}

.back-button:hover {
  background: rgba(255, 255, 255, 0.3);
  transform: translateX(-5px);
}

.header {
  text-align: center;
  color: white;
  margin-bottom: 30px;
  margin-top: 60px;
}

.header h1 {
  font-size: 48px;
  margin-bottom: 10px;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
}

.subtitle {
  font-size: 18px;
  opacity: 0.95;
}

.wheel-display-container {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 40px;
  max-width: 1200px;
  width: 100%;
  margin: 0 auto;
  align-items: start;
}

/* Input Section */
.input-section {
  background: rgba(255, 255, 255, 0.95);
  padding: 30px;
  border-radius: 20px;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.2);
}

.names-counter {
  text-align: center;
  font-size: 18px;
  font-weight: 600;
  color: #667eea;
  margin-bottom: 15px;
  padding: 10px;
  background: #f8f9fa;
  border-radius: 10px;
}

.names-list {
  max-height: 400px;
  overflow-y: auto;
  margin-bottom: 20px;
}

.name-item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 12px 16px;
  margin-bottom: 10px;
  background: #f8f9fa;
  border-radius: 10px;
  border-left: 4px solid #667eea;
  transition: all 0.3s ease;
}

.name-item:hover {
  transform: translateX(5px);
  box-shadow: 0 3px 10px rgba(0, 0, 0, 0.1);
}

.name-number {
  background: #667eea;
  color: white;
  font-weight: 700;
  padding: 4px 10px;
  border-radius: 50%;
  font-size: 14px;
  min-width: 28px;
  text-align: center;
}

.name-text {
  font-size: 16px;
  font-weight: 500;
  color: #333;
}

.empty-names {
  text-align: center;
  padding: 40px;
  color: #999;
}

.action-buttons {
  display: flex;
  gap: 10px;
}

.spin-button {
  width: 100%;
  padding: 14px 24px;
  border: none;
  border-radius: 10px;
  font-size: 16px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  background: linear-gradient(135deg, #FFD700, #FFA500);
  color: #2c3e50;
}

.spin-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 5px 20px rgba(255, 215, 0, 0.5);
}

/* Wheel Section */
.wheel-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 30px;
  margin-bottom: 20px;
  width: 100%;
}

/* Result Section */
.result-section {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 500px;
}

.winner-display-full {
  background: rgba(255, 255, 255, 0.95);
  padding: 40px;
  border-radius: 20px;
  text-align: center;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.2);
  animation: winner-appear 0.5s ease;
  width: 100%;
}

.waiting-display {
  background: rgba(255, 255, 255, 0.95);
  padding: 40px;
  border-radius: 20px;
  text-align: center;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.2);
  width: 100%;
}

.waiting-icon {
  font-size: 80px;
  margin-bottom: 20px;
  animation: pulse 2s ease-in-out infinite;
}

@keyframes pulse {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.1); }
}

.waiting-title {
  font-size: 28px;
  color: #667eea;
  margin-bottom: 15px;
  font-weight: 700;
}

.waiting-text {
  font-size: 18px;
  color: #666;
  margin-bottom: 20px;
  line-height: 1.6;
}

.selection-stats {
  display: flex;
  gap: 20px;
  justify-content: center;
  margin-bottom: 20px;
}

.stat-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
}

.stat-label {
  font-size: 14px;
  color: #666;
  font-weight: 600;
}

.stat-value {
  font-size: 28px;
  font-weight: 800;
  padding: 8px 16px;
  border-radius: 10px;
}

.stat-value.available {
  background: linear-gradient(135deg, #00B894, #00D4AA);
  color: white;
}

.stat-value.selected {
  background: linear-gradient(135deg, #FD79A8, #F8B500);
  color: white;
}

.reset-button {
  margin-top: 10px;
  padding: 10px 20px;
  background: #ff6b6b;
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
}

.reset-button:hover {
  background: #ff5252;
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(255, 107, 107, 0.4);
}

.reset-button-alt {
  margin-top: 25px;
  padding: 14px 28px;
  background: linear-gradient(135deg, #ff6b6b, #ff5252);
  color: white;
  border: none;
  border-radius: 12px;
  font-size: 16px;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 5px 20px rgba(255, 107, 107, 0.4);
}

.reset-button-alt:hover {
  background: linear-gradient(135deg, #ff5252, #ff4040);
  transform: translateY(-3px);
  box-shadow: 0 8px 30px rgba(255, 107, 107, 0.6);
}

.reset-button-alt:active {
  transform: translateY(-1px);
}

/* Large Spin Button */
.spin-button-large {
  padding: 20px 40px;
  border: none;
  border-radius: 15px;
  font-size: 24px;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.3s ease;
  background: linear-gradient(135deg, #FFD700, #FFA500);
  color: #2c3e50;
  box-shadow: 
    0 8px 25px rgba(255, 215, 0, 0.4),
    inset 0 1px 0 rgba(255, 255, 255, 0.5);
  min-width: 300px;
  position: relative;
  overflow: hidden;
}

.spin-button-large::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.4), transparent);
  transition: left 0.5s ease;
}

.spin-button-large:hover::before {
  left: 100%;
}

.spin-button-large:hover:not(:disabled) {
  transform: translateY(-3px);
  box-shadow: 0 12px 35px rgba(255, 215, 0, 0.6);
}

.spin-button-large:disabled {
  opacity: 0.7;
  cursor: not-allowed;
}

.wheel-container {
  position: relative;
  width: 550px;
  max-width: 100%;
  height: 550px;
  max-height: 90vw;
  padding: 25px;
  background: linear-gradient(135deg, rgba(255, 255, 255, 0.1), rgba(255, 255, 255, 0.05));
  border-radius: 50%;
  box-shadow: 
    0 20px 60px rgba(0, 0, 0, 0.2),
    inset 0 0 50px rgba(255, 255, 255, 0.1);
  transition: all 0.3s ease;
  margin: 0 auto;
  display: flex;
  align-items: center;
  justify-content: center;
  aspect-ratio: 1 / 1;
}

.wheel-container:hover {
  transform: scale(1.02);
}

.wheel {
  width: 100%;
  height: 100%;
  max-width: 500px;
  max-height: 500px;
  filter: drop-shadow(0 10px 30px rgba(0, 0, 0, 0.3));
  transition: transform 3s cubic-bezier(0.17, 0.67, 0.12, 0.99);
  transform-origin: center center;
  animation: wheelGlow 3s ease-in-out infinite;
  aspect-ratio: 1;
  display: block;
  shape-rendering: geometricPrecision;
  margin: auto;
}

@keyframes wheelGlow {
  0%, 100% {
    filter: drop-shadow(0 10px 30px rgba(0, 0, 0, 0.3));
  }
  50% {
    filter: drop-shadow(0 15px 40px rgba(102, 126, 234, 0.5));
  }
}

.wheel-container.spinning .wheel {
  animation: 
    wheelGlow 3s ease-in-out infinite,
    spinGlow 3s ease-in-out;
}

@keyframes spinGlow {
  0% {
    filter: drop-shadow(0 10px 30px rgba(0, 0, 0, 0.3));
  }
  50% {
    filter: drop-shadow(0 20px 50px rgba(102, 126, 234, 0.8));
  }
  100% {
    filter: drop-shadow(0 10px 30px rgba(0, 0, 0, 0.3));
  }
}

.slice {
  stroke: rgba(0, 0, 0, 0.3);
  stroke-width: 2;
  transition: all 0.3s ease;
  filter: drop-shadow(0 2px 4px rgba(0, 0, 0, 0.2));
  shape-rendering: geometricPrecision;
  stroke-linejoin: bevel;
}

.slice-text {
  font-weight: 700;
  fill: #fff;
  stroke: #2c3e50;
  stroke-width: 0.5px;
  paint-order: stroke fill;
  pointer-events: none;
  letter-spacing: 0.5px;
  text-anchor: middle;
  dominant-baseline: central;
}

.pointer {
  position: absolute;
  top: -15px;
  left: 50%;
  transform: translateX(-50%);
  width: 0;
  height: 0;
  border-left: 18px solid transparent;
  border-right: 18px solid transparent;
  border-top: 35px solid #ff6b6b;
  z-index: 10;
  filter: drop-shadow(0 5px 15px rgba(255, 107, 107, 0.6));
  animation: pointerPulse 2s ease-in-out infinite;
  margin-left: 0;
}

.pointer-glow {
  position: absolute;
  top: -20px;
  left: 50%;
  transform: translateX(-50%);
  width: 0;
  height: 0;
  border-left: 25px solid transparent;
  border-right: 25px solid transparent;
  border-top: 45px solid rgba(255, 107, 107, 0.4);
  z-index: 9;
  animation: pointerGlow 2s ease-in-out infinite;
  margin-left: 0;
}

.center-dot {
  animation: centerSpin 3s linear infinite;
}

/* Center Spin Button */
.spin-button-center {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 120px;
  height: 120px;
  min-width: 120px;
  min-height: 120px;
  max-width: 120px;
  max-height: 120px;
  border: none;
  border-radius: 50%;
  background: linear-gradient(135deg, #667eea, #764ba2);
  color: white;
  font-size: 20px;
  font-weight: 800;
  cursor: pointer;
  transition: all 0.3s ease;
  z-index: 100;
  box-shadow: 
    0 8px 25px rgba(102, 126, 234, 0.6),
    inset 0 0 20px rgba(255, 255, 255, 0.2);
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: visible;
  aspect-ratio: 1;
  flex-shrink: 0;
}

.spin-button-center:hover:not(:disabled) {
  transform: translate(-50%, -50%) scale(1.1);
  box-shadow: 
    0 12px 35px rgba(102, 126, 234, 0.8),
    inset 0 0 25px rgba(255, 255, 255, 0.3);
}

.spin-button-center:active:not(:disabled) {
  transform: translate(-50%, -50%) scale(0.95);
}

.spin-button-center:disabled {
  opacity: 0.8;
  cursor: not-allowed;
  background: linear-gradient(135deg, #999, #888);
}

.spin-center-text {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 5px;
  width: 100%;
  height: 100%;
  position: relative;
}

.spinner-icon {
  font-size: 32px;
  line-height: 1;
  animation: iconBounce 1s ease-in-out infinite;
}

@keyframes iconBounce {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-5px); }
}

.spinner-label {
  font-size: 14px;
  font-weight: 700;
  letter-spacing: 1px;
}

@keyframes pointerPulse {
  0%, 100% { 
    filter: drop-shadow(0 5px 15px rgba(255, 107, 107, 0.6));
  }
  50% { 
    filter: drop-shadow(0 8px 25px rgba(255, 107, 107, 0.9));
    transform: translateX(-50%) scale(1.05);
  }
}

@keyframes pointerGlow {
  0%, 100% { 
    opacity: 0.3;
    transform: translateX(-50%) scale(1);
  }
  50% { 
    opacity: 0.6;
    transform: translateX(-50%) scale(1.2);
  }
}

@keyframes centerSpin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.winner-icon {
  font-size: 80px;
  margin-bottom: 15px;
  animation: bounce 1s ease infinite;
}

@keyframes winner-appear {
  from {
    opacity: 0;
    transform: scale(0.9);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

@keyframes bounce {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-15px); }
}

.winner-title {
  font-size: 28px;
  color: #667eea;
  margin-bottom: 20px;
  font-weight: 700;
}

.winner-name {
  font-size: 48px;
  font-weight: 800;
  color: #2c3e50;
  margin: 0;
}

/* Mobile Responsive */
@media (max-width: 1024px) {
  .wheel-display-container {
    grid-template-columns: 1fr;
    max-width: 100%;
    gap: 30px;
  }

  .wheel-container {
    width: 80vw;
    height: 80vw;
    max-width: 500px;
    max-height: 500px;
    aspect-ratio: 1 / 1;
    padding: 20px;
    margin: 0 auto;
  }

  .spin-button-center {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 100px;
    height: 100px;
    min-width: 100px;
    min-height: 100px;
    max-width: 100px;
    max-height: 100px;
  }
  
  .spin-button-center:hover:not(:disabled) {
    transform: translate(-50%, -50%) scale(1.05);
  }
  
  .spin-button-center:active:not(:disabled) {
    transform: translate(-50%, -50%) scale(0.95);
  }
  
  .spinner-icon {
    font-size: 28px;
  }
  
  .spinner-label {
    font-size: 12px;
  }

  .result-section {
    min-height: auto;
  }

  .winner-display-full,
  .waiting-display {
    padding: 30px;
  }
}

@media (max-width: 768px) {
  .spin-wheel-page {
    padding: 20px 10px;
    min-height: 100vh;
  }

  .back-button {
    top: 10px;
    left: 10px;
    padding: 8px 16px;
    font-size: 13px;
    border-radius: 20px;
  }

  .header {
    margin-top: 50px;
    margin-bottom: 25px;
  }

  .header h1 {
    font-size: 28px;
  }

  .subtitle {
    font-size: 14px;
  }

  .wheel-display-container {
    gap: 20px;
  }

  .wheel-section {
    margin-bottom: 0;
    margin-top: 20px;
    padding-top: 30px;
    width: 100%;
    overflow: visible;
  }

  .wheel-container {
    width: 90vw;
    height: 90vw;
    max-width: 90vw;
    max-height: 90vw;
    padding: 15px;
    margin: 10px auto 0;
    aspect-ratio: 1 / 1;
  }

  .wheel {
    width: 100%;
    height: 100%;
    max-width: 100%;
    max-height: 100%;
    aspect-ratio: 1 / 1;
  }

  .spin-button-center {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 90px;
    height: 90px;
    min-width: 90px;
    min-height: 90px;
    max-width: 90px;
    max-height: 90px;
  }
  
  .spin-button-center:hover:not(:disabled) {
    transform: translate(-50%, -50%) scale(1.05);
  }
  
  .spin-button-center:active:not(:disabled) {
    transform: translate(-50%, -50%) scale(0.95);
  }
  
  .spinner-icon {
    font-size: 24px;
  }
  
  .spinner-label {
    font-size: 11px;
  }

  .result-section {
    min-height: auto;
  }

  .winner-display-full,
  .waiting-display {
    padding: 25px 20px;
  }

  .winner-icon, .waiting-icon {
    font-size: 60px;
    margin-bottom: 15px;
  }

  .winner-title, .waiting-title {
    font-size: 22px;
  }

  .winner-name {
    font-size: 32px;
  }

  .waiting-text {
    font-size: 15px;
  }

  .selection-stats {
    gap: 15px;
    margin-bottom: 15px;
    flex-wrap: wrap;
  }

  .stat-label {
    font-size: 12px;
  }

  .stat-value {
    font-size: 22px;
    padding: 6px 12px;
  }

  .reset-button-alt {
    font-size: 14px;
    padding: 12px 24px;
    width: 100%;
  }

  .reset-button {
    font-size: 13px;
    padding: 8px 16px;
  }
  
  .slice-text {
    stroke-width: 0.3px;
    letter-spacing: 0.3px;
  }
  
  .pointer {
    border-left-width: 16px;
    border-right-width: 16px;
    border-top-width: 32px;
  }
  
  .pointer-glow {
    border-left-width: 22px;
    border-right-width: 22px;
    border-top-width: 40px;
  }
}

@media (max-width: 480px) {
  .spin-wheel-page {
    padding: 15px 5px;
  }

  .back-button {
    top: 8px;
    left: 8px;
    padding: 6px 12px;
    font-size: 12px;
  }

  .header {
    margin-top: 35px;
    margin-bottom: 20px;
  }

  .header h1 {
    font-size: 24px;
  }

  .subtitle {
    font-size: 13px;
  }

  .wheel-display-container {
    gap: 15px;
  }

  .wheel-section {
    margin-top: 15px;
    padding-top: 25px;
    width: 100%;
    overflow: visible;
  }

  .wheel-container {
    width: 92vw;
    height: 92vw;
    max-width: 92vw;
    max-height: 92vw;
    padding: 12px;
    margin: 10px auto 0;
    aspect-ratio: 1 / 1;
  }

  .wheel {
    width: 100%;
    height: 100%;
    max-width: 100%;
    max-height: 100%;
    aspect-ratio: 1 / 1;
  }

  .spin-button-center {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 80px;
    height: 80px;
    min-width: 80px;
    min-height: 80px;
    max-width: 80px;
    max-height: 80px;
    font-size: 14px;
  }
  
  .spin-button-center:hover:not(:disabled) {
    transform: translate(-50%, -50%) scale(1.05);
  }
  
  .spin-button-center:active:not(:disabled) {
    transform: translate(-50%, -50%) scale(0.95);
  }
  
  .spinner-icon {
    font-size: 20px;
  }
  
  .spinner-label {
    font-size: 9px;
  }

  .winner-display-full,
  .waiting-display {
    padding: 20px 15px;
  }

  .winner-icon, .waiting-icon {
    font-size: 50px;
    margin-bottom: 12px;
  }

  .winner-title, .waiting-title {
    font-size: 20px;
    margin-bottom: 12px;
  }

  .winner-name {
    font-size: 28px;
  }

  .waiting-text {
    font-size: 14px;
  }

  .selection-stats {
    gap: 10px;
  }

  .stat-label {
    font-size: 11px;
  }

  .stat-value {
    font-size: 20px;
    padding: 5px 10px;
  }

  .reset-button-alt {
    font-size: 13px;
    padding: 10px 20px;
  }

  .slice-text {
    stroke-width: 0.2px;
    letter-spacing: 0.2px;
  }
  
  .pointer {
    border-left-width: 12px;
    border-right-width: 12px;
    border-top-width: 25px;
    top: -10px;
  }

  .pointer-glow {
    border-left-width: 16px;
    border-right-width: 16px;
    border-top-width: 32px;
    top: -13px;
  }
}

@media (max-width: 360px) {
  .header h1 {
    font-size: 22px;
  }

  .wheel-container {
    width: 95vw;
    height: 95vw;
    max-width: 95vw;
    max-height: 95vw;
    padding: 10px;
    aspect-ratio: 1 / 1;
  }

  .spin-button-center {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 70px;
    height: 70px;
    min-width: 70px;
    min-height: 70px;
    max-width: 70px;
    max-height: 70px;
  }

  .spin-button-center:hover:not(:disabled) {
    transform: translate(-50%, -50%) scale(1.05);
  }
  
  .spin-button-center:active:not(:disabled) {
    transform: translate(-50%, -50%) scale(0.95);
  }

  .spinner-icon {
    font-size: 16px;
  }

  .spinner-label {
    font-size: 7px;
  }

  .winner-icon, .waiting-icon {
    font-size: 45px;
  }

  .winner-title, .waiting-title {
    font-size: 18px;
  }

  .winner-name {
    font-size: 24px;
  }

  .wheel-container:hover {
    transform: none;
  }
  
  .slice-text {
    stroke-width: 0.15px;
    letter-spacing: 0.1px;
  }
  
  .pointer {
    border-left-width: 10px;
    border-right-width: 10px;
    border-top-width: 20px;
    top: -8px;
  }

  .pointer-glow {
    border-left-width: 14px;
    border-right-width: 14px;
    border-top-width: 28px;
    top: -11px;
  }
}

/* Touch optimizations */
@media (hover: none) and (pointer: coarse) {
  .wheel-container:hover {
    transform: none;
  }

  .spin-button-center:hover:not(:disabled) {
    transform: translate(-50%, -50%) scale(1);
  }

  .back-button:hover {
    transform: none;
  }
}
</style>
