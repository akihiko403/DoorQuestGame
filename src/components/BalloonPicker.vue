<template>
  <div class="balloon-picker-page">
    <!-- Back button -->
    <button class="back-button" @click="$emit('goToGame')">
      ← Back to HOME
    </button>

    <!-- Header -->
    <header class="header">
      <h1>🎈 BALLOON PICKER 🎈</h1>
      <p class="subtitle">Click on a balloon to reveal the name inside!</p>
    </header>

    <!-- Reset Button -->
    <div class="reset-container">
      <button @click="resetGame" class="reset-button">
        🎲 Reset & Shuffle
      </button>
      <div class="stats">
        <span class="stat-text">
          Revealed: <strong>{{ revealedCount }}</strong> / <strong>{{ balloons.length }}</strong>
        </span>
      </div>
    </div>

    <!-- Balloons Container -->
    <div class="balloons-container">
      <div
        v-for="(balloon, index) in balloons"
        :key="balloon.id"
        class="balloon-wrapper"
        :style="{ animationDelay: `${index * 0.1}s` }"
      >
        <div
          class="balloon"
          :class="{ 
            'revealed': balloon.revealed,
            'popping': balloon.popping
          }"
          :style="{ 
            '--balloon-color': balloon.color,
            '--balloon-color-dark': balloon.colorDark
          }"
          @click="revealBalloon(index)"
        >
          <div v-if="!balloon.revealed" class="balloon-content">
            <div class="balloon-shape">
              <span class="balloon-emoji">🎈</span>
            </div>
            <div class="balloon-string"></div>
          </div>
          
          <div v-else class="balloon-content revealed-content">
            <div class="name-display">
              <span class="name-text">{{ balloon.name }}</span>
              <div class="confetti">
                <span v-for="i in 6" :key="i" class="confetti-piece" :style="{ animationDelay: `${i * 0.1}s` }">🎉</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- All Revealed Message -->
    <div v-if="allRevealed" class="celebration-message">
      <div class="celebration-icon">🎊</div>
      <h2>All balloons revealed!</h2>
      <p>Click reset to shuffle and play again!</p>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const emit = defineEmits(['goToGame'])

// Names array (same as SpinWheel)
const namesData = [
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
]

// Vibrant balloon colors
const balloonColors = [
  { main: '#FF6B6B', dark: '#E55555' },
  { main: '#4ECDC4', dark: '#3DB3A3' },
  { main: '#45B7D1', dark: '#3590B8' },
  { main: '#FFA07A', dark: '#E89260' },
  { main: '#98D8C8', dark: '#7FB8A3' },
  { main: '#F7DC6F', dark: '#E0C75F' },
  { main: '#BB8FCE', dark: '#A975D2' },
  { main: '#85C1E2', dark: '#6AA1C2' },
  { main: '#F8B500', dark: '#E09400' },
  { main: '#6C5CE7', dark: '#5845C7' },
  { main: '#FD79A8', dark: '#E56192' },
  { main: '#00B894', dark: '#009877' },
  { main: '#0984E3', dark: '#0872C3' },
  { main: '#E17055', dark: '#C55A3F' },
  { main: '#A29BFE', dark: '#8E8ADE' },
  { main: '#00D2D3', dark: '#00BDBD' },
  { main: '#FF9F43', dark: '#E88F2F' },
  { main: '#5F27CD', dark: '#5324A1' },
  { main: '#00D8FF', dark: '#00C2E8' },
  { main: '#FFA5A5', dark: '#E89595' },
  { main: '#00FF88', dark: '#00E079' },
  { main: '#FFC312', dark: '#E8AF0A' },
  { main: '#EA2027', dark: '#D21618' },
  { main: '#1289A7', dark: '#0F7597' },
  { main: '#9980FA', dark: '#8B70D4' },
  { main: '#48C9B0', dark: '#36B398' },
  { main: '#F368E0', dark: '#E350C3' },
  { main: '#FF9FF3', dark: '#E88FC7' },
  { main: '#54A0FF', dark: '#4A7FDD' },
  { main: '#FF6348', dark: '#E55235' },
  { main: '#2ED573', dark: '#26C565' },
  { main: '#FFA502', dark: '#E89500' },
  { main: '#FC427B', dark: '#E13D6B' },
  { main: '#55E6C1', dark: '#45CEB1' },
  { main: '#CAD3C8', dark: '#B5C3B0' }
]

const balloons = ref([])

// Shuffle array function
const shuffleArray = (array) => {
  const shuffled = [...array]
  for (let i = shuffled.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]]
  }
  return shuffled
}

// Initialize balloons
const initializeBalloons = () => {
  const shuffledNames = shuffleArray(namesData)
  balloons.value = shuffledNames.map((name, index) => ({
    id: index,
    name,
    color: balloonColors[index % balloonColors.length].main,
    colorDark: balloonColors[index % balloonColors.length].dark,
    revealed: false,
    popping: false
  }))
}

const revealBalloon = (index) => {
  if (balloons.value[index].revealed) return
  
  balloons.value[index].popping = true
  
  setTimeout(() => {
    balloons.value[index].revealed = true
    balloons.value[index].popping = false
  }, 300)
}

const resetGame = () => {
  balloons.value.forEach(balloon => {
    balloon.revealed = false
    balloon.popping = false
  })
  initializeBalloons()
}

const revealedCount = computed(() => {
  return balloons.value.filter(b => b.revealed).length
})

const allRevealed = computed(() => {
  return balloons.value.length > 0 && revealedCount.value === balloons.value.length
})

onMounted(() => {
  initializeBalloons()
})
</script>

<style scoped>
.balloon-picker-page {
  min-height: 100vh;
  padding: 40px 20px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  display: flex;
  flex-direction: column;
  align-items: center;
  position: relative;
  overflow-y: auto;
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

.reset-container {
  display: flex;
  align-items: center;
  gap: 20px;
  margin-bottom: 30px;
  flex-wrap: wrap;
  justify-content: center;
}

.reset-button {
  padding: 14px 28px;
  background: linear-gradient(135deg, #FFD700, #FFA500);
  color: #2c3e50;
  border: none;
  border-radius: 12px;
  font-size: 16px;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 5px 20px rgba(255, 215, 0, 0.4);
}

.reset-button:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 30px rgba(255, 215, 0, 0.6);
  background: linear-gradient(135deg, #FFA500, #FFD700);
}

.stats {
  background: rgba(255, 255, 255, 0.2);
  backdrop-filter: blur(10px);
  padding: 12px 24px;
  border-radius: 12px;
  color: white;
  font-size: 16px;
}

.stat-text strong {
  font-size: 18px;
  color: #FFD700;
}

.balloons-container {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(130px, 1fr));
  gap: 20px;
  max-width: 1600px;
  width: 100%;
  padding: 20px;
  margin: 0 auto;
  min-height: auto;
}

.balloon-wrapper {
  animation: balloonFloat 3s ease-in-out infinite;
}

@keyframes balloonFloat {
  0%, 100% {
    transform: translateY(0px);
  }
  50% {
    transform: translateY(-15px);
  }
}

.balloon {
  cursor: pointer;
  transition: all 0.3s ease;
  position: relative;
  width: 100%;
  aspect-ratio: 1;
  display: flex;
  align-items: center;
  justify-content: center;
}

.balloon:not(.revealed):hover {
  transform: scale(1.1);
}

.balloon.popping {
  animation: balloonPop 0.3s ease-out;
}

@keyframes balloonPop {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.3) rotate(5deg);
  }
  100% {
    transform: scale(1);
  }
}

.balloon-content {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  position: relative;
}

.balloon-shape {
  width: 120px;
  height: 140px;
  background: linear-gradient(135deg, var(--balloon-color), var(--balloon-color-dark));
  border-radius: 50% 50% 50% 50% / 60% 60% 40% 40%;
  position: relative;
  box-shadow: 
    0 10px 30px rgba(0, 0, 0, 0.3),
    inset -10px -10px 30px rgba(0, 0, 0, 0.1),
    inset 10px 10px 30px rgba(255, 255, 255, 0.3);
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
}

.balloon-shape::before {
  content: '';
  position: absolute;
  bottom: -10px;
  left: 50%;
  transform: translateX(-50%);
  width: 80%;
  height: 40px;
  background: linear-gradient(135deg, var(--balloon-color), var(--balloon-color-dark));
  clip-path: polygon(30% 0%, 70% 0%, 100% 100%, 0% 100%);
  opacity: 0.8;
}

.balloon-emoji {
  font-size: 60px;
  z-index: 1;
  filter: drop-shadow(2px 2px 4px rgba(0, 0, 0, 0.2));
}

.balloon-string {
  width: 2px;
  height: 60px;
  background: linear-gradient(to bottom, #ccc, #999);
  margin-top: -5px;
  position: relative;
}

.balloon-string::after {
  content: '';
  position: absolute;
  bottom: -5px;
  left: 50%;
  transform: translateX(-50%);
  width: 8px;
  height: 8px;
  background: #666;
  border-radius: 50%;
}

.revealed-content {
  background: rgba(255, 255, 255, 0.95);
  border-radius: 20px;
  padding: 20px;
  width: 100%;
  min-height: 140px;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.2);
  animation: revealAnimation 0.5s ease-out;
  position: relative;
  overflow: hidden;
}

@keyframes revealAnimation {
  0% {
    opacity: 0;
    transform: scale(0.5);
  }
  50% {
    transform: scale(1.1);
  }
  100% {
    opacity: 1;
    transform: scale(1);
  }
}

.name-display {
  position: relative;
  z-index: 2;
  text-align: center;
}

.name-text {
  font-size: 20px;
  font-weight: 800;
  color: #2c3e50;
  display: block;
  text-transform: capitalize;
  letter-spacing: 1px;
}

.confetti {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 100%;
  height: 100%;
  pointer-events: none;
}

.confetti-piece {
  position: absolute;
  font-size: 20px;
  animation: confettiFall 1s ease-out forwards;
  opacity: 0;
}

.confetti-piece:nth-child(1) { top: -20px; left: 10%; }
.confetti-piece:nth-child(2) { top: -20px; left: 30%; }
.confetti-piece:nth-child(3) { top: -20px; left: 50%; }
.confetti-piece:nth-child(4) { top: -20px; left: 70%; }
.confetti-piece:nth-child(5) { top: -20px; left: 90%; }
.confetti-piece:nth-child(6) { top: -20px; left: 50%; }

@keyframes confettiFall {
  0% {
    opacity: 1;
    transform: translateY(0) rotate(0deg);
  }
  70% {
    opacity: 1;
    transform: translateY(80px) rotate(360deg);
  }
  100% {
    opacity: 0;
    transform: translateY(100px) rotate(450deg);
  }
}

.celebration-message {
  margin-top: 40px;
  background: rgba(255, 255, 255, 0.95);
  padding: 40px;
  border-radius: 20px;
  text-align: center;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.2);
  animation: celebrationAppear 0.5s ease;
}

@keyframes celebrationAppear {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.celebration-icon {
  font-size: 80px;
  margin-bottom: 20px;
  animation: bounce 1s ease infinite;
}

@keyframes bounce {
  0%, 100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-20px);
  }
}

.celebration-message h2 {
  font-size: 32px;
  color: #667eea;
  margin-bottom: 10px;
  font-weight: 700;
}

.celebration-message p {
  font-size: 18px;
  color: #666;
}

/* Mobile Responsive */
@media (max-width: 768px) {
  .balloon-picker-page {
    padding: 20px 10px;
  }

  .back-button {
    top: 10px;
    left: 10px;
    padding: 8px 16px;
    font-size: 13px;
  }

  .header {
    margin-top: 50px;
    margin-bottom: 20px;
  }

  .header h1 {
    font-size: 32px;
  }

  .subtitle {
    font-size: 14px;
  }

  .reset-container {
    margin-bottom: 20px;
    gap: 15px;
  }

  .reset-button {
    padding: 12px 20px;
    font-size: 14px;
  }

  .stats {
    padding: 10px 18px;
    font-size: 14px;
  }

  .balloons-container {
    grid-template-columns: repeat(auto-fill, minmax(110px, 1fr));
    gap: 15px;
    padding: 10px;
    max-width: 100%;
  }

  .balloon-shape {
    width: 100px;
    height: 120px;
  }

  .balloon-emoji {
    font-size: 50px;
  }

  .revealed-content {
    min-height: 120px;
    padding: 15px;
  }

  .name-text {
    font-size: 16px;
  }

  .celebration-message {
    padding: 30px 20px;
    margin-top: 30px;
  }

  .celebration-icon {
    font-size: 60px;
  }

  .celebration-message h2 {
    font-size: 24px;
  }

  .celebration-message p {
    font-size: 16px;
  }
}

@media (max-width: 480px) {
  .header h1 {
    font-size: 24px;
  }

  .balloons-container {
    grid-template-columns: repeat(auto-fill, minmax(95px, 1fr));
    gap: 12px;
    max-width: 100%;
  }

  .balloon-shape {
    width: 80px;
    height: 100px;
  }

  .balloon-emoji {
    font-size: 40px;
  }

  .revealed-content {
    min-height: 100px;
    padding: 12px;
  }

  .name-text {
    font-size: 14px;
  }
}

@media (max-width: 360px) {
  .balloons-container {
    grid-template-columns: repeat(auto-fill, minmax(85px, 1fr));
    gap: 10px;
    max-width: 100%;
  }
}
</style>

