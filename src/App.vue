<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import Door from './components/Door.vue'
import QuestionManager from './components/QuestionManager.vue'

const currentPage = ref('game')
const showMenu = ref(false)
const showWelcome = ref(true)
const questions = ref([
  {
    id: 1,
    question: 'Getting enough sleep and exercise are examples of taking care of your physical health.',
    answer: 'TRUE'
  },
  {
    id: 2,
    question: 'Eating juckfood daily is an example of good physical health practice.',
    answer: 'FALSE'
  },
  {
    id: 3,
    question: 'Hollistic health only means being free form sickness or disease.',
    answer: 'FALSE'
  },
  {
    id: 4,
    question: 'People with good spirittual health never face problems.',
    answer: 'FALSE'
  },
  {
    id: 5,
    question: 'Thingking positively and managing stress are signs of good mental health.',
    answer: 'TRUE'
  },
  {
    id: 6,
    question: 'Mental health  refers to the way a personn thinks, learns, and handles stress.',
    answer: 'TRUE'
  }
])

const toggleMenu = () => {
  showMenu.value = !showMenu.value
}

const closeMenu = () => {
  showMenu.value = false
}

const goToManager = () => {
  showMenu.value = false
  currentPage.value = 'manager'
}

const goToGame = (updatedQuestions) => {
  if (updatedQuestions) {
    questions.value = updatedQuestions
  }
  showMenu.value = false
  currentPage.value = 'game'
  // Show welcome popup when going to game from question manager
  showWelcome.value = true
}

const updateQuestions = (updatedQuestions) => {
  questions.value = [...updatedQuestions]
}

// Close menu when clicking outside
const handleClickOutside = (event) => {
  if (showMenu.value && !event.target.closest('.hamburger-menu')) {
    closeMenu()
  }
}

onMounted(() => {
  document.addEventListener('click', handleClickOutside)
})

onUnmounted(() => {
  document.removeEventListener('click', handleClickOutside)
})

// Door colors array
const doorColors = [
  '#8B4513', // Saddle Brown
  '#228B22', // Forest Green
  '#4169E1', // Royal Blue
  '#DC143C', // Crimson
  '#FF8C00', // Dark Orange
  '#9370DB', // Medium Purple
  '#20B2AA', // Light Sea Green
  '#FF1493', // Deep Pink
  '#32CD32', // Lime Green
  '#FF4500'  // Orange Red
]

const getDoorColor = (doorNumber) => {
  return doorColors[(doorNumber - 1) % doorColors.length]
}

const startGame = () => {
  showWelcome.value = false
}
</script>

<template>
  <div class="app">
    <div v-if="currentPage === 'game'" class="game-page">
      <!-- Floating Door Decorations -->
      <div class="floating-doors">
        <div class="door-decoration door-1">🚪</div>
        <div class="door-decoration door-2">🚪</div>
        <div class="door-decoration door-3">🚪</div>
        <div class="door-decoration door-4">🚪</div>
        <div class="door-decoration door-5">🚪</div>
      </div>

      <header class="header">
          <h1>🚪 DOOR QUEST 🚪</h1>
      
        
        <!-- Hamburger Menu -->
        <div class="hamburger-menu" :class="{ 'active': showMenu }">
          <button class="hamburger-btn" @click="toggleMenu">
            <span class="hamburger-line"></span>
            <span class="hamburger-line"></span>
            <span class="hamburger-line"></span>
          </button>
          
          <div class="dropdown-menu" :class="{ 'show': showMenu }">
            <button class="dropdown-item" @click="goToManager">
              📚 Manage Questions
            </button>
          </div>
        </div>
      </header>

      <div class="doors-container">
        <div v-for="q in questions" :key="q.id" class="door-wrapper">
          <Door 
            :door-number="q.id"
            :question="q.question"
            :answer="q.answer"
            :door-color="getDoorColor(q.id)"
          />
        </div>
      </div>

      <div v-if="questions.length === 0" class="empty-state">
        <p>No questions yet! Click "Manage Questions" to add some!</p>
      </div>

      <!-- Welcome Popup -->
      <div v-if="showWelcome" class="welcome-overlay" @click="startGame">
        <div class="welcome-modal" @click.stop>
          <div class="welcome-header">
            <div class="welcome-icon">🚪</div>
            <h1>Welcome to DOOR QUEST!</h1>
          </div>
          
          <div class="welcome-content">
            <p class="welcome-intro">Embark on an exciting DOOR QUEST adventure where each door holds a mystery question!</p>
            
            <div class="features-list">
              <div class="feature-item">
                <span class="feature-icon">🎯</span>
                <div class="feature-text">
                  <strong>Choose Your Door</strong>
                  <p>Click on any door to reveal a unique question in DOOR QUEST</p>
                </div>
              </div>
              
              <div class="feature-item">
                <span class="feature-icon">🤔</span>
                <div class="feature-text">
                  <strong>Read & Think</strong>
                  <p>Take your time to think about the answer</p>
                </div>
              </div>
              
              <div class="feature-item">
                <span class="feature-icon">✨</span>
                <div class="feature-text">
                  <strong>Reveal Answer</strong>
                  <p>Click the button to see if you're correct!</p>
                </div>
              </div>
              
              <div class="feature-item">
                <span class="feature-icon">📚</span>
                <div class="feature-text">
                  <strong>Manage Questions</strong>
                  <p>Use the menu to add, edit, or delete questions for DOOR QUEST</p>
                </div>
              </div>
            </div>
            
            <div class="game-stats">
              <div class="stat-badge">
                <span class="stat-value">{{ questions.length }}</span>
                <span class="stat-text">Questions Ready</span>
              </div>
              <div class="stat-badge">
                <span class="stat-value">{{ Math.ceil(questions.length / 3) }}</span>
                <span class="stat-text">Door Rows</span>
              </div>
            </div>
          </div>
          
          <div class="welcome-footer">
            <button class="start-button" @click="startGame">
              <span class="btn-icon">🚀</span>
              <span class="btn-text">Start DOOR QUEST</span>
              <div class="btn-shine"></div>
            </button>
            <p class="skip-text" @click="startGame">or press anywhere to start</p>
          </div>
        </div>
      </div>
    </div>

    <div v-else-if="currentPage === 'manager'" class="manager-page">
      <QuestionManager :questions="questions" @goToGame="goToGame" @updateQuestions="updateQuestions" />
    </div>
  </div>
</template>

<style scoped>
.app {
  min-height: 100vh;
  width: 100%;
  overflow-x: hidden;
}

.game-page {
  padding: 40px 20px;
  background: linear-gradient(135deg, #ffecd2 0%, #fcb69f 50%, #a8edea 100%);
  min-height: 100vh;
  position: relative;
  overflow: hidden;
  width: 100%;
  max-width: 100vw;
}

.game-page::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-image: 
    radial-gradient(circle at 20% 20%, rgba(255, 255, 255, 0.1) 0%, transparent 50%),
    radial-gradient(circle at 80% 80%, rgba(255, 255, 255, 0.1) 0%, transparent 50%),
    radial-gradient(circle at 40% 60%, rgba(255, 255, 255, 0.05) 0%, transparent 50%),
    radial-gradient(circle at 60% 40%, rgba(255, 255, 255, 0.05) 0%, transparent 50%);
  animation: background-glow 8s ease-in-out infinite;
  pointer-events: none;
}

.game-page::after {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-image: 
    linear-gradient(45deg, transparent 48%, rgba(255, 255, 255, 0.03) 49%, rgba(255, 255, 255, 0.03) 51%, transparent 52%),
    linear-gradient(-45deg, transparent 48%, rgba(255, 255, 255, 0.03) 49%, rgba(255, 255, 255, 0.03) 51%, transparent 52%);
  background-size: 60px 60px;
  animation: door-pattern 20s linear infinite;
  pointer-events: none;
}

/* Floating Door Decorations */
.floating-doors {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  pointer-events: none;
  z-index: 1;
}

.door-decoration {
  position: absolute;
  font-size: 64px;
  opacity: 0.4;
  animation: float-door 6s ease-in-out infinite;
}

.door-1 {
  top: 15%;
  left: 10%;
  animation-delay: 0s;
}

.door-2 {
  top: 25%;
  right: 15%;
  animation-delay: 1s;
}

.door-3 {
  bottom: 20%;
  left: 20%;
  animation-delay: 2s;
}

.door-4 {
  bottom: 30%;
  right: 10%;
  animation-delay: 3s;
}

.door-5 {
  top: 60%;
  left: 5%;
  animation-delay: 4s;
}

@keyframes float-door {
  0%, 100% { 
    transform: translateY(0px) rotate(0deg);
    opacity: 0.3;
  }
  50% { 
    transform: translateY(-20px) rotate(5deg);
    opacity: 0.5;
  }
}

.header {
  text-align: center;
  color: white;
  margin-bottom: 40px;
  position: relative;
  z-index: 1;
}

.header h1 {
  font-size: 48px;
  margin-bottom: 10px;
  text-shadow: 
    -1px -1px 0 #000,
    1px -1px 0 #000,
    -1px 1px 0 #000,
    1px 1px 0 #000,
    2px 2px 4px rgba(0, 0, 0, 0.3);
}

.subtitle {
  font-size: 18px;
  opacity: 0.95;
  font-weight: 500;
  line-height: 1.6;
  max-width: 600px;
  margin: 0 auto 20px;
  text-shadow: 
    -1px -1px 0 #000,
    1px -1px 0 #000,
    -1px 1px 0 #000,
    1px 1px 0 #000,
    2px 2px 4px rgba(0, 0, 0, 0.3);
  background: rgba(255, 255, 255, 0.1);
  padding: 15px 25px;
  border-radius: 20px;
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.2);
}

/* Hamburger Menu Styles */
.hamburger-menu {
  position: fixed;
  top: 20px;
  left: 20px;
  z-index: 1000;
}

.hamburger-btn {
  background: rgba(0, 0, 0, 0.8);
  border: 2px solid rgba(0, 0, 0, 0.9);
  border-radius: 8px;
  padding: 12px;
  cursor: pointer;
  transition: all 0.3s ease;
  backdrop-filter: blur(10px);
  display: flex;
  flex-direction: column;
  gap: 4px;
  width: 50px;
  height: 50px;
  justify-content: center;
  align-items: center;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
  /* Improve touch feedback */
  -webkit-tap-highlight-color: rgba(255, 255, 255, 0.3);
  touch-action: manipulation;
}

.hamburger-btn:hover {
  background: rgba(0, 0, 0, 0.9);
  border-color: rgba(0, 0, 0, 1);
  transform: scale(1.05);
  box-shadow: 0 6px 16px rgba(0, 0, 0, 0.4);
}

.hamburger-line {
  width: 24px;
  height: 3px;
  background: white;
  border-radius: 2px;
  transition: all 0.3s ease;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.5);
}

.hamburger-menu.active .hamburger-line:nth-child(1) {
  transform: rotate(45deg) translate(6px, 6px);
}

.hamburger-menu.active .hamburger-line:nth-child(2) {
  opacity: 0;
}

.hamburger-menu.active .hamburger-line:nth-child(3) {
  transform: rotate(-45deg) translate(6px, -6px);
}

.dropdown-menu {
  position: absolute;
  top: 60px;
  left: 0;
  background: white;
  border-radius: 12px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
  border: 1px solid rgba(0, 0, 0, 0.1);
  min-width: 180px;
  opacity: 0;
  visibility: hidden;
  transform: translateY(-10px);
  transition: all 0.3s ease;
  overflow: hidden;
  animation: float 3s ease-in-out infinite;
  z-index: 1001;
}

.dropdown-menu.show {
  opacity: 1;
  visibility: visible;
  transform: translateY(0);
  animation: float-show 3s ease-in-out infinite;
}

.dropdown-item {
  width: 100%;
  padding: 14px 16px;
  border: none;
  background: none;
  color: #2c3e50;
  font-size: 15px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  text-align: left;
  border-bottom: 1px solid rgba(0, 0, 0, 0.05);
  white-space: nowrap;
}

.dropdown-item:last-child {
  border-bottom: none;
}

.dropdown-item:hover {
  background: #f8f9fa;
  color: #3498db;
  transform: translateX(-5px);
}

.doors-container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
  max-width: 900px;
  margin: 0 auto;
  position: relative;
  z-index: 1;
  width: 100%;
  padding: 0 10px;
}

.door-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
}

.empty-state {
  text-align: center;
  color: white;
  font-size: 20px;
  margin-top: 60px;
  padding: 40px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 16px;
  max-width: 600px;
  margin: 60px auto 0;
  position: relative;
  z-index: 2;
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.2);
  text-shadow: 
    -1px -1px 0 #000,
    1px -1px 0 #000,
    -1px 1px 0 #000,
    1px 1px 0 #000,
    2px 2px 4px rgba(0, 0, 0, 0.3);
}

.manager-page {
  height: 100vh;
  overflow: hidden;
}

/* Background Animations */
@keyframes background-glow {
  0%, 100% { 
    opacity: 0.8;
    transform: scale(1);
  }
  50% { 
    opacity: 1;
    transform: scale(1.1);
  }
}

@keyframes door-pattern {
  0% { 
    transform: translateX(0) translateY(0);
  }
  100% { 
    transform: translateX(60px) translateY(60px);
  }
}

/* Floating Animation */
@keyframes float {
  0%, 100% { 
    transform: translateY(-10px) translateY(0px); 
  }
  50% { 
    transform: translateY(-10px) translateY(-3px); 
  }
}

@keyframes float-show {
  0%, 100% { 
    transform: translateY(0px) translateY(0px); 
  }
  50% { 
    transform: translateY(0px) translateY(-3px); 
  }
}

/* Welcome Popup */
.welcome-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.8);
  backdrop-filter: blur(10px);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 2000;
  animation: fadeIn 0.5s ease;
  padding: 20px;
  box-sizing: border-box;
  overflow-y: auto;
  -webkit-overflow-scrolling: touch;
}

.welcome-modal {
  background: linear-gradient(135deg, #667eea, #764ba2);
  border-radius: 30px;
  max-width: 650px;
  width: 100%;
  max-height: 90vh;
  box-shadow: 
    0 30px 80px rgba(0, 0, 0, 0.4),
    0 0 0 1px rgba(255, 255, 255, 0.2),
    inset 0 1px 0 rgba(255, 255, 255, 0.3);
  animation: modalSlideIn 0.6s ease;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  margin: auto;
}

.welcome-header {
  text-align: center;
  padding: 40px 40px 25px;
  background: rgba(255, 255, 255, 0.1);
  border-bottom: 1px solid rgba(255, 255, 255, 0.2);
  flex-shrink: 0;
}

.welcome-icon {
  font-size: 70px;
  margin-bottom: 15px;
  animation: doorBounce 2s ease-in-out infinite;
}

.welcome-header h1 {
  font-size: 32px;
  color: white;
  margin: 0;
  font-weight: 800;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
  letter-spacing: -1px;
  line-height: 1.2;
}

.welcome-content {
  padding: 30px 40px;
  flex: 1;
  overflow-y: auto;
}

.welcome-intro {
  text-align: center;
  color: white;
  font-size: 16px;
  line-height: 1.5;
  margin-bottom: 25px;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.3);
}

.features-list {
  display: grid;
  gap: 15px;
  margin-bottom: 25px;
}

.feature-item {
  display: flex;
  align-items: flex-start;
  gap: 15px;
  background: rgba(255, 255, 255, 0.1);
  padding: 15px;
  border-radius: 12px;
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.2);
  transition: all 0.3s ease;
}

.feature-item:hover {
  background: rgba(255, 255, 255, 0.15);
  transform: translateX(5px);
}

.feature-icon {
  font-size: 28px;
  flex-shrink: 0;
}

.feature-text {
  flex: 1;
}

.feature-text strong {
  display: block;
  color: white;
  font-size: 16px;
  margin-bottom: 5px;
  font-weight: 700;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.3);
}

.feature-text p {
  margin: 0;
  color: rgba(255, 255, 255, 0.8);
  font-size: 14px;
  line-height: 1.5;
}

.game-stats {
  display: flex;
  justify-content: center;
  gap: 20px;
  margin-top: 20px;
}

.stat-badge {
  background: rgba(255, 255, 255, 0.15);
  backdrop-filter: blur(10px);
  padding: 12px 20px;
  border-radius: 12px;
  text-align: center;
  border: 1px solid rgba(255, 255, 255, 0.2);
}

.stat-value {
  display: block;
  font-size: 28px;
  font-weight: 800;
  color: #FFD700;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
  margin-bottom: 4px;
}

.stat-text {
  display: block;
  font-size: 12px;
  color: rgba(255, 255, 255, 0.8);
  text-transform: uppercase;
  letter-spacing: 1px;
  font-weight: 600;
}

.welcome-footer {
  text-align: center;
  padding: 25px 40px 30px;
  background: rgba(0, 0, 0, 0.1);
  flex-shrink: 0;
}

.start-button {
  width: 100%;
  padding: 18px;
  background: linear-gradient(135deg, #FFD700, #FFA500);
  color: #2c3e50;
  border: none;
  border-radius: 14px;
  font-size: 18px;
  font-weight: 800;
  cursor: pointer;
  transition: all 0.3s ease;
  text-transform: uppercase;
  letter-spacing: 1.5px;
  position: relative;
  overflow: hidden;
  box-shadow: 0 8px 25px rgba(255, 215, 0, 0.4);
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  /* Improve touch feedback */
  -webkit-tap-highlight-color: rgba(255, 193, 7, 0.3);
  touch-action: manipulation;
  min-height: 56px;
}

.start-button:hover {
  transform: translateY(-3px);
  box-shadow: 0 15px 40px rgba(255, 215, 0, 0.6);
  background: linear-gradient(135deg, #FFA500, #FFD700);
}

.btn-icon {
  font-size: 24px;
}

.btn-shine {
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.4), transparent);
  transition: left 0.6s ease;
}

.start-button:hover .btn-shine {
  left: 100%;
}

.skip-text {
  margin-top: 15px;
  color: rgba(255, 255, 255, 0.7);
  font-size: 14px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.skip-text:hover {
  color: white;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes modalSlideIn {
  from {
    opacity: 0;
    transform: translateY(-50px) scale(0.9);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

@keyframes doorBounce {
  0%, 100% { transform: translateY(0) scale(1); }
  50% { transform: translateY(-10px) scale(1.1); }
}

/* Mobile Responsive Styles */
@media (max-width: 1024px) {
  .doors-container {
    grid-template-columns: repeat(2, 1fr);
    gap: 25px;
    max-width: 700px;
    z-index: 1;
  }
  
  .hamburger-menu {
    z-index: 1000;
  }
  
  .dropdown-menu {
    z-index: 1001;
  }
}

@media (max-width: 768px) {
  .game-page {
    padding: 20px 15px;
    min-height: 100vh;
    min-height: -webkit-fill-available;
  }
  
  .header {
    margin-bottom: 25px;
    padding-top: 10px;
  }
  
  .header h1 {
    font-size: 32px;
    word-wrap: break-word;
  }
  
  .subtitle {
    font-size: 16px;
    padding: 12px 20px;
    max-width: 90%;
  }
  
  .hamburger-menu {
    top: 15px;
    left: 15px;
    z-index: 1000;
  }
  
  .hamburger-btn {
    width: 45px;
    height: 45px;
    padding: 10px;
  }
  
  .hamburger-line {
    width: 20px;
    height: 2px;
  }
  
  .dropdown-menu {
    min-width: 160px;
    top: 55px;
    z-index: 1001;
  }
  
  .dropdown-item {
    padding: 12px 14px;
    font-size: 14px;
  }
  
  .doors-container {
    grid-template-columns: repeat(2, 1fr);
    max-width: 600px;
    gap: 20px;
    padding: 15px 10px;
    z-index: 1;
    margin-top: 80px; /* Add space for hamburger menu */
    width: 100%;
    box-sizing: border-box;
  }
  
  .door-wrapper {
    gap: 8px;
    width: 100%;
  }
  
  .empty-state {
    padding: 40px 20px;
    margin: 0 15px;
  }
  
  .empty-state p {
    font-size: 16px;
    max-width: 90%;
  }
  
  /* Welcome Modal Mobile */
  .welcome-modal {
    width: 95%;
    max-width: 500px;
    max-height: 95vh;
  }
  
  .welcome-header {
    padding: 30px 25px 20px;
  }
  
  .welcome-icon {
    font-size: 60px;
  }
  
  .welcome-header h1 {
    font-size: 28px;
    line-height: 1.2;
  }
  
  .welcome-content {
    padding: 25px 20px;
  }
  
  .welcome-intro {
    font-size: 16px;
    margin-bottom: 20px;
  }
  
  .features-list {
    gap: 15px;
    margin-bottom: 20px;
  }
  
  .feature-item {
    padding: 15px;
    gap: 12px;
  }
  
  .feature-icon {
    font-size: 24px;
  }
  
  .feature-text strong {
    font-size: 14px;
  }
  
  .feature-text p {
    font-size: 13px;
  }
  
  .game-stats {
    gap: 15px;
    margin-top: 15px;
  }
  
  .stat-badge {
    padding: 10px 15px;
  }
  
  .stat-value {
    font-size: 24px;
  }
  
  .stat-text {
    font-size: 11px;
  }
  
  .welcome-footer {
    padding: 20px 25px 25px;
  }
  
  .start-button {
    padding: 16px;
    font-size: 18px;
    letter-spacing: 1px;
  }
  
  .skip-text {
    font-size: 13px;
    margin-top: 12px;
  }
}

@media (max-width: 640px) {
  .doors-container {
    grid-template-columns: 1fr;
    max-width: 350px;
    gap: 20px;
    z-index: 1;
    margin-top: 80px; /* Add space for hamburger menu */
    padding: 10px 5px;
    width: 100%;
  }
  
  .header h1 {
    font-size: 28px;
    line-height: 1.2;
  }
  
  .subtitle {
    font-size: 14px;
    padding: 10px 15px;
  }
  
  /* Welcome Modal Small Mobile */
  .welcome-modal {
    width: 98%;
    margin: 10px;
    max-height: 98vh;
  }
  
  .welcome-header {
    padding: 25px 20px 15px;
  }
  
  .welcome-icon {
    font-size: 50px;
    margin-bottom: 10px;
  }
  
  .welcome-header h1 {
    font-size: 24px;
  }
  
  .welcome-content {
    padding: 20px 15px;
  }
  
  .welcome-intro {
    font-size: 14px;
    margin-bottom: 15px;
  }
  
  .features-list {
    gap: 12px;
    margin-bottom: 15px;
  }
  
  .feature-item {
    padding: 12px;
    gap: 10px;
  }
  
  .feature-icon {
    font-size: 20px;
  }
  
  .feature-text strong {
    font-size: 13px;
  }
  
  .feature-text p {
    font-size: 12px;
  }
  
  .game-stats {
    flex-direction: column;
    gap: 10px;
    margin-top: 10px;
  }
  
  .stat-badge {
    padding: 8px 12px;
  }
  
  .stat-value {
    font-size: 20px;
  }
  
  .stat-text {
    font-size: 10px;
  }
  
  .welcome-footer {
    padding: 15px 20px 20px;
  }
  
  .start-button {
    padding: 14px;
    font-size: 16px;
    letter-spacing: 0.5px;
  }
}

@media (max-width: 480px) {
  .game-page {
    padding: 15px 10px;
  }
  
  .header {
    margin-bottom: 20px;
  }
  
  .header h1 {
    font-size: 24px;
  }
  
  .doors-container {
    max-width: 280px;
    gap: 15px;
    padding: 10px;
    z-index: 1;
    margin-top: 80px; /* Add space for hamburger menu */
  }
  
  .empty-state {
    padding: 30px 15px;
    margin: 0 10px;
  }
  
  .empty-state p {
    font-size: 14px;
  }
  
  .hamburger-menu {
    top: 10px;
    left: 10px;
    z-index: 1000;
  }
  
  .hamburger-btn {
    width: 40px;
    height: 40px;
    padding: 8px;
  }
  
  .hamburger-line {
    width: 18px;
    height: 2px;
  }
  
  .dropdown-menu {
    top: 50px;
    min-width: 140px;
    z-index: 1001;
  }
  
  .dropdown-item {
    padding: 10px 12px;
    font-size: 13px;
  }
  
  /* Ultra Small Welcome Modal */
  .welcome-modal {
    width: 100%;
    margin: 5px;
    max-height: 100vh;
    border-radius: 20px;
  }
  
  .welcome-header {
    padding: 20px 15px 10px;
  }
  
  .welcome-icon {
    font-size: 40px;
    margin-bottom: 8px;
  }
  
  .welcome-header h1 {
    font-size: 20px;
  }
  
  .welcome-content {
    padding: 15px 10px;
  }
  
  .welcome-intro {
    font-size: 13px;
    margin-bottom: 12px;
  }
  
  .features-list {
    gap: 10px;
    margin-bottom: 12px;
  }
  
  .feature-item {
    padding: 10px;
    gap: 8px;
  }
  
  .feature-icon {
    font-size: 18px;
  }
  
  .feature-text strong {
    font-size: 12px;
  }
  
  .feature-text p {
    font-size: 11px;
  }
  
  .game-stats {
    gap: 8px;
    margin-top: 8px;
  }
  
  .stat-badge {
    padding: 6px 10px;
  }
  
  .stat-value {
    font-size: 18px;
  }
  
  .stat-text {
    font-size: 9px;
  }
  
  .welcome-footer {
    padding: 12px 15px 15px;
  }
  
  .start-button {
    padding: 12px;
    font-size: 14px;
    letter-spacing: 0.5px;
  }
  
  .skip-text {
    font-size: 12px;
    margin-top: 10px;
  }
}

/* Landscape orientation optimizations for game page */
@media (max-width: 768px) and (orientation: landscape) {
  .game-page {
    padding: 15px 10px;
  }
  
  .header {
    margin-bottom: 15px;
    padding-top: 5px;
  }
  
  .header h1 {
    font-size: 24px;
    margin-bottom: 5px;
  }
  
  .doors-container {
    grid-template-columns: repeat(3, 1fr);
    max-width: 95%;
    gap: 15px;
    margin-top: 60px;
    padding: 10px 5px;
  }
  
  /* Welcome modal adjustments for landscape */
  .welcome-modal {
    max-height: 95vh;
    max-width: 85%;
    flex-direction: row;
    align-items: stretch;
  }
  
  .welcome-header {
    padding: 20px 15px;
    flex-shrink: 0;
    width: 35%;
    display: flex;
    flex-direction: column;
    justify-content: center;
  }
  
  .welcome-icon {
    font-size: 50px;
    margin-bottom: 10px;
  }
  
  .welcome-header h1 {
    font-size: 24px;
  }
  
  .welcome-content {
    padding: 20px 15px;
    width: 65%;
    overflow-y: auto;
  }
  
  .welcome-intro {
    font-size: 14px;
    margin-bottom: 15px;
  }
  
  .features-list {
    gap: 10px;
  }
  
  .feature-item {
    padding: 10px;
  }
  
  .feature-text strong {
    font-size: 13px;
  }
  
  .feature-text p {
    font-size: 12px;
  }
  
  .game-stats {
    flex-direction: row;
    gap: 10px;
    margin-top: 10px;
  }
  
  .stat-badge {
    padding: 8px 12px;
    flex: 1;
  }
  
  .stat-value {
    font-size: 20px;
  }
  
  .stat-text {
    font-size: 10px;
  }
  
  .welcome-footer {
    padding: 15px 20px;
  }
  
  .start-button {
    padding: 12px;
    font-size: 16px;
  }
  
  .skip-text {
    font-size: 11px;
    margin-top: 8px;
  }
  
  .floating-doors .door-decoration {
    font-size: 48px;
  }
}

/* Touch interaction improvements */
@media (hover: none) and (pointer: coarse) {
  /* This targets touch devices */
  .hamburger-btn:active {
    transform: scale(0.95);
  }
  
  .dropdown-item:active {
    background: #e0e0e0;
  }
  
  .start-button:active {
    transform: translateY(0);
  }
  
  /* Remove hover effects on touch devices */
  .hamburger-btn:hover {
    background: rgba(0, 0, 0, 0.8);
  }
  
  .door:hover {
    transform: none;
  }
}

/* Prevent zoom on double tap (iOS Safari) */
@media (max-width: 768px) {
  * {
    touch-action: manipulation;
  }
}

</style>