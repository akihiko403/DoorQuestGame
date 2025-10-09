<script setup>
import { ref } from 'vue'

const props = defineProps({
  doorNumber: {
    type: Number,
    required: true
  },
  question: {
    type: String,
    default: ''
  },
  answer: {
    type: String,
    default: ''
  },
  doorColor: {
    type: String,
    default: '#5a3d2b'
  }
})

const isOpen = ref(false)
const showAnswer = ref(false)

const openDoor = () => {
  if (!props.question) return
  isOpen.value = true
  showAnswer.value = false
}

const closeDoor = () => {
  isOpen.value = false
  showAnswer.value = false
}

const revealAnswer = () => {
  showAnswer.value = true
}

// Helper function to darken colors
const darkenColor = (color, percent) => {
  // Convert hex to RGB
  const hex = color.replace('#', '')
  const r = parseInt(hex.substr(0, 2), 16)
  const g = parseInt(hex.substr(2, 2), 16)
  const b = parseInt(hex.substr(4, 2), 16)
  
  // Darken each component
  const newR = Math.floor(r * (1 - percent / 100))
  const newG = Math.floor(g * (1 - percent / 100))
  const newB = Math.floor(b * (1 - percent / 100))
  
  // Convert back to hex
  return `#${newR.toString(16).padStart(2, '0')}${newG.toString(16).padStart(2, '0')}${newB.toString(16).padStart(2, '0')}`
}
</script>

<template>
  <div class="door-container">
    <div class="door" :class="{ 'door-open': isOpen }" @click="openDoor">
      <div class="door-frame"></div>
      <div class="door-front">
        <div class="door-panel door-panel-top"></div>
        <div class="door-number">{{ doorNumber }}</div>
        <div class="door-panel door-panel-bottom"></div>
        <div class="door-handle"></div>
        <div class="door-keyhole"></div>
        <div class="door-rivet rivet-1"></div>
        <div class="door-rivet rivet-2"></div>
        <div class="door-rivet rivet-3"></div>
        <div class="door-rivet rivet-4"></div>
      </div>
    </div>
    
    <transition name="question-fade">
      <div v-if="isOpen" class="question-panel">
        <button class="close-btn" @click="closeDoor">✕</button>
        
        <div class="question-content">
          <h2>Question {{ doorNumber }}</h2>
          <p class="question">{{ question }}</p>
          
          <button 
            v-if="!showAnswer" 
            class="reveal-btn" 
            @click="revealAnswer"
          >
            Reveal Answer
          </button>
          
          <transition name="answer-slide">
            <div v-if="showAnswer" class="answer-box">
              <h3>Answer:</h3>
              <p class="answer">{{ answer }}</p>
            </div>
          </transition>
        </div>
      </div>
    </transition>
  </div>
</template>

<style scoped>
.door-container {
  position: relative;
  display: inline-block;
  margin: 20px;
  width: 100%;
  max-width: 180px;
  box-sizing: border-box;
}

.door-frame {
  position: absolute;
  top: -10px;
  left: -10px;
  right: -10px;
  bottom: -10px;
  background: linear-gradient(135deg, #4a3728 0%, #2d2318 50%, #4a3728 100%);
  border-radius: 16px 16px 8px 8px;
  box-shadow: 
    0 20px 50px rgba(0, 0, 0, 0.6),
    inset 0 2px 4px rgba(255, 255, 255, 0.1);
  z-index: -1;
}

.door {
  width: 100%;
  max-width: 180px;
  height: 260px;
  perspective: 1000px;
  cursor: pointer;
  transition: transform 0.3s ease;
  /* Improve touch interaction */
  -webkit-tap-highlight-color: transparent;
  touch-action: manipulation;
}

.door:hover {
  transform: scale(1.05);
}

@media (hover: none) and (pointer: coarse) {
  /* Remove hover scale on touch devices to prevent jumpiness */
  .door:hover {
    transform: none;
  }
  
  .door:active {
    transform: scale(0.98);
  }
}

.door-front {
  width: 100%;
  height: 100%;
  background: v-bind('`linear-gradient(180deg, ${doorColor} 0%, ${darkenColor(doorColor, 20)} 50%, ${doorColor} 100%)`');
  border: 6px solid v-bind('darkenColor(doorColor, 40)');
  border-radius: 12px 12px 4px 4px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  position: relative;
  box-shadow: 
    0 15px 40px rgba(0, 0, 0, 0.5),
    inset 0 2px 4px rgba(255, 255, 255, 0.1),
    inset 0 -2px 4px rgba(0, 0, 0, 0.3);
  transition: all 0.3s ease;
  overflow: hidden;
}

.door-front::before {
  content: '';
  position: absolute;
  top: 20px;
  left: 15px;
  right: 15px;
  bottom: 20px;
  border: 3px solid rgba(139, 69, 19, 0.4);
  border-radius: 8px;
  pointer-events: none;
}

.door-front::after {
  content: '';
  position: absolute;
  top: 50%;
  left: 15px;
  right: 15px;
  height: 3px;
  background: rgba(60, 40, 20, 0.6);
  pointer-events: none;
}

.door-open .door-front {
  transform: rotateY(-85deg);
  transform-origin: left;
}

.door-number {
  font-size: 56px;
  font-weight: bold;
  color: #ffd700;
  text-shadow: 
    2px 2px 4px rgba(0, 0, 0, 0.8),
    0 0 10px rgba(255, 215, 0, 0.5),
    0 0 20px rgba(255, 215, 0, 0.3);
  z-index: 2;
  filter: drop-shadow(0 4px 8px rgba(0, 0, 0, 0.5));
}

.door-handle {
  position: absolute;
  right: 25px;
  top: 50%;
  transform: translateY(-50%);
  width: 18px;
  height: 35px;
  background: linear-gradient(135deg, #d4af37 0%, #a68523 50%, #d4af37 100%);
  border-radius: 8px;
  box-shadow: 
    0 4px 8px rgba(0, 0, 0, 0.4),
    inset 0 1px 2px rgba(255, 255, 255, 0.3),
    inset 0 -1px 2px rgba(0, 0, 0, 0.3);
  z-index: 2;
}

.door-handle::before {
  content: '';
  position: absolute;
  top: 50%;
  left: -8px;
  transform: translateY(-50%);
  width: 8px;
  height: 8px;
  background: radial-gradient(circle, #d4af37, #8b6914);
  border-radius: 50%;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
}

.door-handle::after {
  content: '';
  position: absolute;
  top: -5px;
  left: 50%;
  transform: translateX(-50%);
  width: 10px;
  height: 3px;
  background: #8b6914;
  border-radius: 2px;
}

.door-panel {
  position: absolute;
  left: 20px;
  right: 20px;
  height: 55px;
  background: rgba(0, 0, 0, 0.15);
  border: 2px solid rgba(90, 61, 43, 0.5);
  border-radius: 6px;
  box-shadow: 
    inset 0 2px 4px rgba(0, 0, 0, 0.3),
    inset 0 -1px 2px rgba(255, 255, 255, 0.05);
}

.door-panel-top {
  top: 25px;
}

.door-panel-bottom {
  bottom: 25px;
}

.door-keyhole {
  position: absolute;
  right: 30px;
  bottom: 80px;
  width: 10px;
  height: 10px;
  background: #1a1a1a;
  border-radius: 50% 50% 50% 50% / 60% 60% 40% 40%;
  box-shadow: 
    inset 0 2px 4px rgba(0, 0, 0, 0.8),
    0 1px 2px rgba(255, 215, 0, 0.2);
  z-index: 3;
}

.door-keyhole::after {
  content: '';
  position: absolute;
  top: 8px;
  left: 50%;
  transform: translateX(-50%);
  width: 4px;
  height: 8px;
  background: #1a1a1a;
  clip-path: polygon(50% 0%, 100% 100%, 0% 100%);
}

.door-rivet {
  position: absolute;
  width: 8px;
  height: 8px;
  background: radial-gradient(circle at 30% 30%, #8b7355, #4a3728);
  border-radius: 50%;
  box-shadow: 
    inset 0 1px 2px rgba(255, 255, 255, 0.3),
    inset 0 -1px 2px rgba(0, 0, 0, 0.5),
    0 1px 2px rgba(0, 0, 0, 0.3);
  z-index: 2;
}

.rivet-1 {
  top: 30px;
  left: 25px;
}

.rivet-2 {
  top: 30px;
  right: 25px;
}

.rivet-3 {
  bottom: 30px;
  left: 25px;
}

.rivet-4 {
  bottom: 30px;
  right: 25px;
}

.door-front:hover .door-handle {
  background: linear-gradient(135deg, #ffd700 0%, #d4af37 50%, #ffd700 100%);
  box-shadow: 
    0 4px 12px rgba(255, 215, 0, 0.4),
    inset 0 1px 2px rgba(255, 255, 255, 0.4),
    inset 0 -1px 2px rgba(0, 0, 0, 0.3);
}

.door-front:hover .door-keyhole {
  box-shadow: 
    inset 0 2px 4px rgba(0, 0, 0, 0.8),
    0 0 8px rgba(255, 215, 0, 0.4);
}

.question-panel {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background: #4CAF50;
  padding: 6px;
  border-radius: 24px;
  box-shadow: 
    0 25px 80px rgba(0, 0, 0, 0.3),
    0 0 0 4px #FF9800,
    0 0 0 8px #E91E63,
    0 0 0 12px #2196F3;
  max-width: 650px;
  width: 90%;
  max-height: 90vh;
  z-index: 1000;
  color: white;
  overflow: hidden;
  overflow-y: auto;
  -webkit-overflow-scrolling: touch;
}

@media (prefers-reduced-motion: reduce) {
  .question-panel {
    animation: none;
  }
  
  .question,
  .answer-box,
  .reveal-btn {
    animation: none !important;
  }
}

.close-btn {
  position: absolute;
  top: 20px;
  right: 20px;
  background: #F44336;
  color: white;
  border: 3px solid #FFD54F;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  cursor: pointer;
  font-size: 26px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
  box-shadow: 
    0 8px 20px rgba(244, 67, 54, 0.5),
    inset 0 3px 0 rgba(255, 255, 255, 0.3);
  z-index: 10;
  animation: close-spin 4s linear infinite;
  /* Improve touch feedback */
  -webkit-tap-highlight-color: rgba(244, 67, 54, 0.3);
  touch-action: manipulation;
}

@media (prefers-reduced-motion: reduce) {
  .close-btn {
    animation: none;
  }
}

.close-btn:hover {
  background: #D32F2F;
  border-color: #FFEB3B;
  transform: rotate(360deg) scale(1.2);
  box-shadow: 
    0 10px 25px rgba(244, 67, 54, 0.7),
    inset 0 3px 0 rgba(255, 255, 255, 0.4);
}

.question-content {
  text-align: center;
  padding: 40px;
  background: #FFFFFF;
  border-radius: 20px;
  color: #333333;
  border: 3px solid #FFEB3B;
}

.question-content h2 {
  color: #E91E63;
  margin-bottom: 30px;
  font-size: 40px;
  font-weight: 900;
  text-shadow: 3px 3px 0px #FF9800, 6px 6px 0px #2196F3;
  animation: title-wiggle 2s ease-in-out infinite;
}

.question {
  font-size: 26px;
  line-height: 1.7;
  margin: 35px 0;
  padding: 30px;
  background: #FFF3E0;
  border-radius: 20px;
  border: 5px solid #FF5722;
  color: #333333;
  box-shadow: 
    0 15px 35px rgba(255, 87, 34, 0.4),
    inset 0 3px 0 #FFFFFF;
  position: relative;
  overflow: hidden;
  animation: question-shake 4s ease-in-out infinite;
}

.question::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 12px;
  height: 100%;
  background: #FF5722;
  border-radius: 6px 0 0 6px;
  animation: accent-pulse 2s ease-in-out infinite;
}

.question::after {
  content: '🤔';
  position: absolute;
  bottom: 15px;
  right: 20px;
  font-size: 52px;
  animation: thinking 3s infinite;
  pointer-events: none;
}

.reveal-btn {
  background: #FF5722;
  color: white;
  border: 4px solid #FFC107;
  padding: 20px 50px;
  border-radius: 35px;
  font-size: 24px;
  font-weight: 900;
  cursor: pointer;
  transition: all 0.4s ease;
  box-shadow: 
    0 10px 25px rgba(255, 87, 34, 0.5),
    inset 0 3px 0 rgba(255, 255, 255, 0.3);
  text-transform: uppercase;
  letter-spacing: 2px;
  position: relative;
  overflow: hidden;
  animation: button-bounce 2s ease-in-out infinite;
  /* Improve touch feedback */
  -webkit-tap-highlight-color: rgba(255, 87, 34, 0.3);
  touch-action: manipulation;
  min-height: 60px;
}

@media (prefers-reduced-motion: reduce) {
  .reveal-btn {
    animation: none;
  }
}

.reveal-btn::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: rgba(255, 193, 7, 0.3);
  transition: left 0.6s ease;
}

.reveal-btn::after {
  content: '🚀';
  position: absolute;
  top: 50%;
  right: 15px;
  transform: translateY(-50%);
  font-size: 24px;
  animation: rocket-launch 2s infinite;
}

.reveal-btn:hover::before {
  left: 100%;
}

.reveal-btn:hover {
  background: #E64A19;
  border-color: #FFD54F;
  transform: translateY(-5px) scale(1.1);
  box-shadow: 
    0 15px 35px rgba(255, 87, 34, 0.7),
    inset 0 3px 0 rgba(255, 255, 255, 0.4);
}

.answer-box {
  margin-top: 35px;
  padding: 30px;
  background: #E8F5E8;
  border-radius: 20px;
  border: 5px solid #4CAF50;
  box-shadow: 
    0 15px 35px rgba(76, 175, 80, 0.4),
    inset 0 3px 0 rgba(255, 255, 255, 0.8);
  position: relative;
  overflow: hidden;
  animation: answer-celebration 3s ease-in-out infinite;
}

.answer-box::before {
  content: '🎊';
  position: absolute;
  top: 15px;
  right: 20px;
  font-size: 32px;
  animation: confetti 2s infinite;
}

.answer-box h3 {
  color: #2E7D32;
  margin-bottom: 20px;
  font-size: 30px;
  font-weight: 900;
  text-shadow: 2px 2px 0px #FFD54F, 4px 4px 0px #FF5722;
  animation: success-dance 2s ease-in-out infinite;
}

.answer {
  font-size: 24px;
  line-height: 1.7;
  color: #1B5E20;
  font-weight: 700;
  background: #FFFFFF;
  padding: 25px;
  border-radius: 16px;
  box-shadow: 
    inset 0 3px 6px rgba(0, 0, 0, 0.1),
    0 6px 12px rgba(0, 0, 0, 0.1);
  border-left: 8px solid #4CAF50;
  animation: answer-highlight 2s ease-in-out infinite;
}

/* Animations */
.question-fade-enter-active, .question-fade-leave-active {
  transition: all 0.4s ease;
}

.question-fade-enter-from {
  opacity: 0;
  transform: translate(-50%, -45%) scale(0.9);
}

.question-fade-leave-to {
  opacity: 0;
  transform: translate(-50%, -45%) scale(0.9);
}

.answer-slide-enter-active, .answer-slide-leave-active {
  transition: all 0.4s ease;
}

.answer-slide-enter-from {
  opacity: 0;
  transform: translateY(20px);
}

.answer-slide-leave-to {
  opacity: 0;
  transform: translateY(-20px);
}

/* Overlay */
.question-panel::before {
  content: '';
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(33, 150, 243, 0.2);
  z-index: -1;
  backdrop-filter: blur(5px);
}

/* Fun Student-Friendly Animations */


@keyframes title-wiggle {
  0%, 100% { transform: rotate(0deg); }
  25% { transform: rotate(-2deg); }
  75% { transform: rotate(2deg); }
}

@keyframes question-shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-2px); }
  75% { transform: translateX(2px); }
}

@keyframes accent-pulse {
  0%, 100% { background-color: #FF5722; }
  50% { background-color: #FF9800; }
}

@keyframes thinking {
  0%, 100% { transform: rotate(0deg) scale(1); }
  50% { transform: rotate(10deg) scale(1.1); }
}

@keyframes button-bounce {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-3px); }
}

@keyframes rocket-launch {
  0%, 100% { transform: translateY(-50%) rotate(0deg); }
  50% { transform: translateY(-60%) rotate(15deg); }
}

@keyframes answer-celebration {
  0%, 100% { border-color: #4CAF50; }
  50% { border-color: #8BC34A; }
}

@keyframes confetti {
  0%, 100% { transform: rotate(0deg) scale(1); }
  25% { transform: rotate(-15deg) scale(1.2); }
  75% { transform: rotate(15deg) scale(1.2); }
}

@keyframes success-dance {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-5px); }
}

@keyframes answer-highlight {
  0%, 100% { border-left-color: #4CAF50; }
  50% { border-left-color: #8BC34A; }
}

@keyframes close-spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

/* Mobile Responsive Styles for Door Component */
@media (max-width: 1024px) {
  .door {
    width: 150px;
    height: 210px;
  }
  
  .door-number {
    font-size: 44px;
  }
  
  .door-panel {
    height: 42px;
  }
}

@media (max-width: 768px) {
  .door-container {
    margin: 15px auto;
    max-width: 130px;
  }
  
  .door {
    width: 100%;
    max-width: 130px;
    height: 180px;
  }
  
  .door-number {
    font-size: 36px;
  }
  
  .door-panel {
    height: 35px;
    left: 12px;
    right: 12px;
  }
  
  .door-handle {
    right: 20px;
    width: 16px;
    height: 30px;
  }
  
  .door-keyhole {
    right: 25px;
    bottom: 70px;
    width: 8px;
    height: 8px;
  }
  
  .door-rivet {
    width: 6px;
    height: 6px;
  }
  
  .rivet-1, .rivet-2 {
    top: 25px;
  }
  
  .rivet-3, .rivet-4 {
    bottom: 25px;
  }
  
  .rivet-1, .rivet-3 {
    left: 20px;
  }
  
  .rivet-2, .rivet-4 {
    right: 20px;
  }
  
  /* Question Panel Mobile */
  .question-panel {
    max-width: 96%;
    width: 96%;
    padding: 12px;
    border-radius: 18px;
    margin: 8px;
    box-sizing: border-box;
    max-height: 95vh;
    overflow-y: auto;
  }
  
  .close-btn {
    top: 15px;
    right: 15px;
    width: 40px;
    height: 40px;
    font-size: 20px;
  }
  
  .question-content {
    padding: 18px 12px;
    border-radius: 15px;
    margin: 0;
  }
  
  .question-content h2 {
    font-size: 28px;
    margin-bottom: 20px;
  }
  
  .question {
    font-size: 16px;
    padding: 16px;
    margin: 18px 0;
    line-height: 1.4;
  }
  
  .question::after {
    font-size: 40px;
    bottom: 10px;
    right: 15px;
  }
  
  .reveal-btn {
    padding: 14px 20px;
    font-size: 16px;
    width: 100%;
    max-width: 260px;
    margin: 0 auto;
    min-height: 50px;
  }
  
  .reveal-btn::after {
    font-size: 20px;
    right: 10px;
  }
  
  .answer-box {
    margin-top: 25px;
    padding: 20px;
  }
  
  .answer-box::before {
    font-size: 24px;
    top: 10px;
    right: 15px;
  }
  
  .answer-box h3 {
    font-size: 22px;
    margin-bottom: 15px;
  }
  
  .answer {
    font-size: 18px;
    padding: 20px;
  }
}

@media (max-width: 640px) {
  .door-container {
    margin: 12px auto;
    max-width: 120px;
  }
  
  .door {
    width: 100%;
    max-width: 120px;
    height: 165px;
  }
  
  .door-number {
    font-size: 32px;
  }
  
  .door-panel {
    height: 32px;
    left: 10px;
    right: 10px;
  }
  
  .door-handle {
    right: 18px;
    width: 14px;
    height: 26px;
  }
  
  .door-keyhole {
    right: 22px;
    bottom: 60px;
    width: 7px;
    height: 7px;
  }
  
  /* Question Panel Small Mobile */
  .question-panel {
    max-width: 98%;
    width: 98%;
    padding: 8px;
    border-radius: 15px;
    margin: 4px;
    box-sizing: border-box;
    max-height: 96vh;
    overflow-y: auto;
  }
  
  .close-btn {
    top: 12px;
    right: 12px;
    width: 35px;
    height: 35px;
    font-size: 18px;
  }
  
  .question-content {
    padding: 14px 10px;
    border-radius: 12px;
    margin: 0;
  }
  
  .question-content h2 {
    font-size: 24px;
    margin-bottom: 15px;
  }
  
  .question {
    font-size: 16px;
    padding: 15px;
    margin: 20px 0;
  }
  
  .question::after {
    font-size: 32px;
    bottom: 8px;
    right: 12px;
  }
  
  .reveal-btn {
    padding: 12px 20px;
    font-size: 15px;
    width: 100%;
    max-width: 240px;
    margin: 0 auto;
    min-height: 48px;
  }
  
  .reveal-btn::after {
    font-size: 18px;
    right: 8px;
  }
  
  .answer-box {
    margin-top: 20px;
    padding: 15px;
  }
  
  .answer-box::before {
    font-size: 20px;
    top: 8px;
    right: 12px;
  }
  
  .answer-box h3 {
    font-size: 18px;
    margin-bottom: 12px;
  }
  
  .answer {
    font-size: 16px;
    padding: 15px;
  }
}

@media (max-width: 480px) {
  .door-container {
    margin: 10px auto;
    max-width: 100px;
  }
  
  .door {
    width: 100%;
    max-width: 100px;
    height: 140px;
  }
  
  .door-number {
    font-size: 28px;
  }
  
  .door-panel {
    height: 28px;
    left: 8px;
    right: 8px;
  }
  
  .door-handle {
    right: 15px;
    width: 12px;
    height: 22px;
  }
  
  .door-keyhole {
    right: 18px;
    bottom: 50px;
    width: 6px;
    height: 6px;
  }
  
  .door-rivet {
    width: 5px;
    height: 5px;
  }
  
  .rivet-1, .rivet-2 {
    top: 20px;
  }
  
  .rivet-3, .rivet-4 {
    bottom: 20px;
  }
  
  .rivet-1, .rivet-3 {
    left: 15px;
  }
  
  .rivet-2, .rivet-4 {
    right: 15px;
  }
  
  /* Question Panel Ultra Small Mobile */
  .question-panel {
    max-width: 99%;
    width: 99%;
    padding: 6px;
    border-radius: 12px;
    margin: 2px;
    box-sizing: border-box;
    max-height: 98vh;
    overflow-y: auto;
  }
  
  .close-btn {
    top: 10px;
    right: 10px;
    width: 30px;
    height: 30px;
    font-size: 16px;
  }
  
  .question-content {
    padding: 12px 8px;
    border-radius: 10px;
    margin: 0;
  }
  
  .question-content h2 {
    font-size: 20px;
    margin-bottom: 12px;
  }
  
  .question {
    font-size: 14px;
    padding: 12px;
    margin: 15px 0;
  }
  
  .question::after {
    font-size: 28px;
    bottom: 6px;
    right: 10px;
  }
  
  .reveal-btn {
    padding: 10px 16px;
    font-size: 14px;
    width: 100%;
    max-width: 220px;
    margin: 0 auto;
    min-height: 44px;
  }
  
  .reveal-btn::after {
    font-size: 16px;
    right: 6px;
  }
  
  .answer-box {
    margin-top: 15px;
    padding: 12px;
  }
  
  .answer-box::before {
    font-size: 18px;
    top: 6px;
    right: 10px;
  }
  
  .answer-box h3 {
    font-size: 16px;
    margin-bottom: 10px;
  }
  
  .answer {
    font-size: 14px;
    padding: 12px;
  }
}

/* Landscape orientation optimizations for Door */
@media (max-width: 768px) and (orientation: landscape) {
  .door-container {
    margin: 10px auto;
  }
  
  .door {
    height: 140px;
  }
  
  .door-number {
    font-size: 28px;
  }
  
  .door-panel {
    height: 28px;
  }
  
  /* Question panel in landscape */
  .question-panel {
    max-height: 90vh;
    width: 95%;
    max-width: 85%;
  }
  
  .question-content {
    padding: 15px 20px;
  }
  
  .question-content h2 {
    font-size: 20px;
    margin-bottom: 12px;
  }
  
  .question {
    font-size: 16px;
    padding: 15px;
    margin: 15px 0;
  }
  
  .question::after {
    font-size: 28px;
  }
  
  .reveal-btn {
    padding: 12px 30px;
    font-size: 16px;
    min-height: 48px;
  }
  
  .answer-box {
    margin-top: 15px;
    padding: 15px;
  }
  
  .answer-box h3 {
    font-size: 18px;
    margin-bottom: 10px;
  }
  
  .answer {
    font-size: 14px;
    padding: 12px;
  }
  
  .close-btn {
    width: 40px;
    height: 40px;
    font-size: 20px;
    top: 10px;
    right: 10px;
  }
}

/* Touch device optimizations */
@media (hover: none) and (pointer: coarse) {
  .reveal-btn:active {
    transform: translateY(-2px) scale(1.05);
  }
  
  .close-btn:active {
    transform: rotate(90deg) scale(1.1);
  }
  
  /* Remove hover effects on touch devices */
  .reveal-btn:hover {
    transform: translateY(0);
  }
  
  .door-front:hover .door-handle {
    background: linear-gradient(135deg, #d4af37 0%, #a68523 50%, #d4af37 100%);
  }
  
  .door-front:hover .door-keyhole {
    box-shadow: 
      inset 0 2px 4px rgba(0, 0, 0, 0.8),
      0 1px 2px rgba(255, 215, 0, 0.2);
  }
}

</style>
