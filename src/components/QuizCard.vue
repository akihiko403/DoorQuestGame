<template>
  <div class="quiz-card-page">
    <!-- Back button -->
    <button class="back-button" @click="$emit('goToGame')">
      ← Back to HOME
    </button>

    <!-- Floating Decorations -->
    <div class="floating-decorations">
      <div class="decoration decoration-1">📝</div>
      <div class="decoration decoration-2">🎓</div>
      <div class="decoration decoration-3">✨</div>
      <div class="decoration decoration-4">🏆</div>
      <div class="decoration decoration-5">📚</div>
    </div>

    <!-- Header -->
    <header class="header">
      <h1>📝 QUIZ CARD 📝</h1>
      <p class="subtitle">Test your knowledge and compete for the top spot!</p>
    </header>

    <!-- Main Content Area -->
    <div class="quiz-content">
      <!-- Name Entry Screen -->
      <div v-if="gameState === 'name-entry'" class="name-entry-screen">
        <div class="entry-card">
          <div class="entry-icon">👤</div>
          <h2 class="entry-title">Welcome to Quiz Card!</h2>
          <p class="entry-description">Enter your name to start the quiz and compete for the top score!</p>
          
          <form @submit.prevent="startQuiz" class="name-form">
            <div class="form-group">
              <label class="form-label">
                <span class="label-icon">✏️</span>
                Your Name
              </label>
              <input
                v-model="playerName"
                type="text"
                placeholder="Enter your name..."
                class="name-input"
                :class="{ 'input-error': nameError }"
                required
                maxlength="30"
                @input="nameError = ''"
              />
              <div v-if="nameError" class="error-message">
                {{ nameError }}
              </div>
            </div>
            <button type="submit" class="start-quiz-button" :disabled="!playerName.trim()">
              <span class="btn-icon">🚀</span>
              <span class="btn-text">Start Quiz</span>
              <div class="btn-shine"></div>
            </button>
          </form>

          <!-- View Leaderboard Button -->
          <button @click="gameState = 'leaderboard'" class="view-leaderboard-btn">
            🏆 View Leaderboard
          </button>
        </div>
      </div>

      <!-- Quiz Screen -->
      <div v-if="gameState === 'quiz'" class="quiz-screen">
        <div class="quiz-card">
          <!-- Progress Bar -->
          <div class="progress-container">
            <div class="progress-text">
              Question {{ currentQuestionIndex + 1 }} of {{ quizQuestions.length }}
            </div>
            <div class="progress-bar">
              <div class="progress-fill" :style="{ width: progressPercentage + '%' }"></div>
            </div>
          </div>

          <!-- Question -->
          <div class="question-container">
            <h2 class="question-number">Question {{ currentQuestionIndex + 1 }}</h2>
            <p class="question-text">{{ currentQuestion.question }}</p>
          </div>

          <!-- Answer Options -->
          <div class="options-container" :key="currentQuestionIndex">
            <button
              v-for="(option, index) in currentOptions"
              :key="`q${currentQuestionIndex}-opt${index}`"
              @click="selectAnswer(option)"
              class="option-button"
              :class="{
                'selected': selectedAnswer === option
              }"
            >
              <span class="option-letter">{{ String.fromCharCode(65 + index) }}</span>
              <span class="option-text">{{ option }}</span>
            </button>
          </div>

          <!-- Next Button -->
          <div v-if="showResult" class="next-container">
            <button @click="nextQuestion" class="next-button">
              <span v-if="currentQuestionIndex < quizQuestions.length - 1">Next Question →</span>
              <span v-else>See Results 🎉</span>
            </button>
          </div>
        </div>
      </div>

      <!-- Score Display Screen -->
      <div v-if="gameState === 'results'" class="results-overlay" @click="closeResults">
        <div class="results-modal" @click.stop>
          <button class="close-results-btn" @click="closeResults">✕</button>
          
          <div class="results-content">
            <div class="results-icon">{{ scoreEmoji }}</div>
            <h2 class="results-title">Quiz Complete!</h2>
            <p class="results-player">{{ playerName }}</p>
            
            <div class="score-display">
              <div class="score-value">{{ score }}</div>
              <div class="score-label">out of {{ quizQuestions.length }}</div>
            </div>

            <div class="score-percentage">
              {{ scorePercentage }}% Correct
            </div>

            <div class="score-message">{{ scoreMessage }}</div>

            <div class="results-actions">
              <button @click="viewLeaderboard" class="action-button primary">
                🏆 View Leaderboard
              </button>
            </div>
          </div>
        </div>
      </div>

      <!-- Leaderboard Screen -->
      <div v-if="gameState === 'leaderboard'" class="leaderboard-screen">
        <div class="leaderboard-card">
          <div class="leaderboard-header">
            <h2 class="leaderboard-title">🏆 Top Scores 🏆</h2>
            <p class="leaderboard-subtitle">Hall of Fame</p>
          </div>

          <div v-if="leaderboard.length > 0" class="leaderboard-list">
            <div
              v-for="(entry, index) in leaderboard"
              :key="index"
              class="leaderboard-entry"
              :class="{ 'top-three': index < 3, 'current-player': entry.isCurrentScore }"
            >
              <div class="rank">
                <span v-if="index === 0" class="medal">🥇</span>
                <span v-else-if="index === 1" class="medal">🥈</span>
                <span v-else-if="index === 2" class="medal">🥉</span>
                <span v-else class="rank-number">{{ index + 1 }}</span>
              </div>
              <div class="player-info">
                <div class="player-name">{{ entry.name }}</div>
                <div class="player-date">{{ formatDate(entry.timestamp) }}</div>
              </div>
              <div class="player-score">
                <div class="score-points">{{ entry.score }}/{{ quizQuestions.length }}</div>
                <div class="score-percent">{{ Math.round((entry.score / quizQuestions.length) * 100) }}%</div>
              </div>
            </div>
          </div>

          <div v-else class="empty-leaderboard">
            <div class="empty-icon">🏆</div>
            <p>No scores yet! Be the first to complete the quiz!</p>
          </div>

          <div class="leaderboard-actions">
            <button @click="gameState = 'name-entry'" class="action-button primary">
              📝 Take Quiz
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const emit = defineEmits(['goToGame'])

const props = defineProps({
  quizQuestions: {
    type: Array,
    required: true
  }
})

// Game state
const gameState = ref('name-entry') // 'name-entry', 'quiz', 'results', 'leaderboard'
const playerName = ref('')
const currentQuestionIndex = ref(0)
const selectedAnswer = ref(null)
const showResult = ref(false)
const score = ref(0)
const leaderboard = ref([])
const lastScore = ref(null)
const nameError = ref('')

// Current question
const currentQuestion = computed(() => {
  return props.quizQuestions[currentQuestionIndex.value]
})

// Shuffled options for each question (generated at quiz start)
const shuffledOptions = ref([])

const currentOptions = computed(() => {
  const opts = shuffledOptions.value[currentQuestionIndex.value]
  return opts && opts.length ? opts : currentQuestion.value.options
})

// Progress percentage
const progressPercentage = computed(() => {
  return ((currentQuestionIndex.value + 1) / props.quizQuestions.length) * 100
})

// Score calculations
const scorePercentage = computed(() => {
  return Math.round((score.value / props.quizQuestions.length) * 100)
})

const scoreEmoji = computed(() => {
  const percent = scorePercentage.value
  if (percent === 100) return '🏆'
  if (percent >= 90) return '🌟'
  if (percent >= 80) return '🎉'
  if (percent >= 70) return '😊'
  if (percent >= 60) return '👍'
  return '💪'
})

const scoreMessage = computed(() => {
  const percent = scorePercentage.value
  if (percent === 100) return 'Perfect Score! Outstanding! 🏆'
  if (percent >= 90) return 'Excellent work! Almost perfect! 🌟'
  if (percent >= 80) return 'Great job! Keep it up! 🎉'
  if (percent >= 70) return 'Good effort! You\'re doing well! 😊'
  if (percent >= 60) return 'Not bad! Room for improvement! 👍'
  return 'Keep practicing! You\'ll do better next time! 💪'
})

// Start quiz
const startQuiz = () => {
  if (!playerName.value.trim()) return
  
  // Clear previous error
  nameError.value = ''
  
  // Check if name already exists in leaderboard
  try {
    const existingScores = JSON.parse(localStorage.getItem('quizCardScores') || '[]')
    const nameExists = existingScores.some(entry => 
      entry.name.toLowerCase() === playerName.value.trim().toLowerCase()
    )
    
    if (nameExists) {
      nameError.value = 'This name has already taken the quiz! Please use a different name.'
      return
    }
  } catch (error) {
    console.error('Error checking existing names:', error)
  }
  
  gameState.value = 'quiz'
  currentQuestionIndex.value = 0
  score.value = 0
  selectedAnswer.value = null
  showResult.value = false

  // Generate shuffled options for each question
  try {
    shuffledOptions.value = props.quizQuestions.map(q => shuffleArray([...(q.options || [])]))
  } catch (err) {
    console.error('Error shuffling options:', err)
    shuffledOptions.value = props.quizQuestions.map(q => [...(q.options || [])])
  }
}

// Select answer
const selectAnswer = (answer) => {
  selectedAnswer.value = answer
  if (!showResult.value) {
    showResult.value = true
  }
}

// Next question
const nextQuestion = () => {
  // Tally score based on final selection for the current question
  if (selectedAnswer.value === currentQuestion.value.correctAnswer) {
    score.value++
  }
  
  if (currentQuestionIndex.value < props.quizQuestions.length - 1) {
    currentQuestionIndex.value++
    selectedAnswer.value = null
    showResult.value = false
  } else {
    // Quiz complete
    finishQuiz()
  }
}

// Finish quiz
const finishQuiz = () => {
  // Save score to localStorage
  saveScore()
  gameState.value = 'results'
}

// Close results and go to leaderboard
const closeResults = () => {
  viewLeaderboard()
}

// View leaderboard
const viewLeaderboard = () => {
  loadLeaderboard()
  gameState.value = 'leaderboard'
}

// Retake quiz
const retakeQuiz = () => {
  gameState.value = 'name-entry'
  currentQuestionIndex.value = 0
  score.value = 0
  selectedAnswer.value = null
  showResult.value = false
  shuffledOptions.value = []
}

// Save score to localStorage
const saveScore = () => {
  const scoreEntry = {
    name: playerName.value.trim(),
    score: score.value,
    timestamp: Date.now(),
    totalQuestions: props.quizQuestions.length
  }
  
  lastScore.value = scoreEntry
  
  try {
    // Get existing scores
    const existingScores = JSON.parse(localStorage.getItem('quizCardScores') || '[]')
    
    // Add new score
    existingScores.push(scoreEntry)
    
    // Sort by score (highest first), then by timestamp (most recent first)
    existingScores.sort((a, b) => {
      if (b.score !== a.score) {
        return b.score - a.score
      }
      return b.timestamp - a.timestamp
    })
    
    // Keep only top 50 scores
    const topScores = existingScores.slice(0, 50)
    
    // Save back to localStorage
    localStorage.setItem('quizCardScores', JSON.stringify(topScores))
  } catch (error) {
    console.error('Error saving score:', error)
  }
}

// Load leaderboard
const loadLeaderboard = () => {
  try {
    const scores = JSON.parse(localStorage.getItem('quizCardScores') || '[]')
    
    // Mark the current player's most recent score
    leaderboard.value = scores.map(entry => ({
      ...entry,
      isCurrentScore: lastScore.value && 
        entry.name === lastScore.value.name && 
        entry.timestamp === lastScore.value.timestamp
    }))
  } catch (error) {
    console.error('Error loading leaderboard:', error)
    leaderboard.value = []
  }
}

// Clear leaderboard
const clearLeaderboard = () => {
  if (confirm('Are you sure you want to clear all scores? This cannot be undone!')) {
    try {
      localStorage.removeItem('quizCardScores')
      leaderboard.value = []
      lastScore.value = null
    } catch (error) {
      console.error('Error clearing leaderboard:', error)
    }
  }
}

// Format date
const formatDate = (timestamp) => {
  const date = new Date(timestamp)
  const now = new Date()
  const diff = now - date
  
  // Less than 1 minute
  if (diff < 60000) {
    return 'Just now'
  }
  
  // Less than 1 hour
  if (diff < 3600000) {
    const minutes = Math.floor(diff / 60000)
    return `${minutes} minute${minutes > 1 ? 's' : ''} ago`
  }
  
  // Less than 24 hours
  if (diff < 86400000) {
    const hours = Math.floor(diff / 3600000)
    return `${hours} hour${hours > 1 ? 's' : ''} ago`
  }
  
  // Less than 7 days
  if (diff < 604800000) {
    const days = Math.floor(diff / 86400000)
    return `${days} day${days > 1 ? 's' : ''} ago`
  }
  
  // Format as date
  return date.toLocaleDateString()
}

// Load leaderboard on mount
onMounted(() => {
  loadLeaderboard()
})

// Fisher–Yates shuffle
function shuffleArray(arr) {
  for (let i = arr.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1))
    const temp = arr[i]
    arr[i] = arr[j]
    arr[j] = temp
  }
  return arr
}
</script>

<style scoped>
.quiz-card-page {
  min-height: 100dvh;
  padding: 40px 20px;
  padding-bottom: 40px;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  position: relative;
  overflow-x: hidden;
}

.quiz-card-page::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-image: 
    radial-gradient(circle at 20% 20%, rgba(255, 255, 255, 0.1) 0%, transparent 50%),
    radial-gradient(circle at 80% 80%, rgba(255, 255, 255, 0.1) 0%, transparent 50%);
  animation: background-glow 8s ease-in-out infinite;
  pointer-events: none;
}

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

/* Floating Decorations */
.floating-decorations {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  pointer-events: none;
  z-index: 1;
}

.decoration {
  position: absolute;
  font-size: 60px;
  opacity: 0.3;
  animation: float-deco 6s ease-in-out infinite;
}

.decoration-1 {
  top: 10%;
  left: 8%;
  animation-delay: 0s;
}

.decoration-2 {
  top: 15%;
  right: 10%;
  animation-delay: 1s;
}

.decoration-3 {
  bottom: 20%;
  left: 15%;
  animation-delay: 2s;
}

.decoration-4 {
  bottom: 25%;
  right: 12%;
  animation-delay: 3s;
}

.decoration-5 {
  top: 50%;
  left: 5%;
  animation-delay: 4s;
}

@keyframes float-deco {
  0%, 100% { 
    transform: translateY(0px) rotate(0deg);
    opacity: 0.2;
  }
  50% { 
    transform: translateY(-30px) rotate(10deg);
    opacity: 0.4;
  }
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
  margin-top: 10px;
  position: relative;
  z-index: 2;
}

.header h1 {
  font-size: 40px;
  margin-bottom: 10px;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
}

.subtitle {
  font-size: 16px;
  opacity: 0.95;
}

.quiz-content {
  width: 100%;
  max-width: 800px;
  position: relative;
  z-index: 2;
}

/* Name Entry Screen */
.name-entry-screen {
  animation: fade-in 0.5s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: calc(100dvh - 220px);
}

.entry-card {
  background: rgba(255, 255, 255, 0.95);
  border-radius: 30px;
  padding: 50px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
  border: 4px solid #FFD700;
  text-align: center;
  margin-bottom: 20px;
}

.entry-icon {
  font-size: 80px;
  margin-bottom: 20px;
  animation: bounce 2s ease-in-out infinite;
}

@keyframes bounce {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-15px); }
}

.entry-title {
  font-size: 36px;
  font-weight: 900;
  color: #667eea;
  margin-bottom: 15px;
}

.entry-description {
  font-size: 18px;
  color: #666;
  margin-bottom: 40px;
  line-height: 1.6;
}

.name-form {
  max-width: 500px;
  margin: 0 auto;
}

.form-group {
  margin-bottom: 30px;
  text-align: left;
}

.form-label {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 18px;
  font-weight: 700;
  color: #2c3e50;
  margin-bottom: 10px;
}

.label-icon {
  font-size: 22px;
}

.name-input {
  width: 100%;
  padding: 15px 20px;
  border: 4px solid #667eea;
  border-radius: 16px;
  font-size: 18px;
  font-family: inherit;
  transition: all 0.3s ease;
  background: #f8f9ff;
  color: #333;
  box-sizing: border-box;
}

.name-input:focus {
  outline: none;
  border-color: #764ba2;
  background: white;
  box-shadow: 0 0 0 4px rgba(118, 75, 162, 0.2);
}

.name-input.input-error {
  border-color: #F44336;
  background: #FFEBEE;
}

.name-input.input-error:focus {
  border-color: #F44336;
  box-shadow: 0 0 0 4px rgba(244, 67, 54, 0.2);
}

.error-message {
  margin-top: 10px;
  padding: 12px;
  background: #FFEBEE;
  border-left: 4px solid #F44336;
  border-radius: 8px;
  color: #C62828;
  font-size: 14px;
  font-weight: 600;
  animation: shake 0.5s ease;
}

@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-10px); }
  75% { transform: translateX(10px); }
}

.start-quiz-button {
  width: 100%;
  padding: 20px;
  background: linear-gradient(135deg, #FFD700, #FFA500);
  color: #2c3e50;
  border: 4px solid #FF5722;
  border-radius: 20px;
  font-size: 22px;
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
  gap: 10px;
}

.start-quiz-button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.start-quiz-button:hover:not(:disabled) {
  transform: translateY(-3px);
  box-shadow: 0 12px 35px rgba(255, 215, 0, 0.6);
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

.start-quiz-button:hover .btn-shine {
  left: 100%;
}

.view-leaderboard-btn {
  margin-top: 20px;
  padding: 12px 24px;
  background: transparent;
  color: #667eea;
  border: 2px solid #667eea;
  border-radius: 12px;
  font-size: 16px;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.3s ease;
}

.view-leaderboard-btn:hover {
  background: #667eea;
  color: white;
  transform: translateY(-2px);
}

/* Quiz Screen */
.quiz-screen {
  animation: fade-in 0.5s ease;
}

.quiz-card {
  background: rgba(255, 255, 255, 0.95);
  border-radius: 30px;
  padding: 40px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
  border: 4px solid #4CAF50;
  max-height: 85vh;
  overflow-y: auto;
  scroll-behavior: smooth;
}

.progress-container {
  margin-bottom: 30px;
}

.progress-text {
  font-size: 16px;
  font-weight: 700;
  color: #667eea;
  margin-bottom: 10px;
  text-align: center;
}

.progress-bar {
  width: 100%;
  height: 12px;
  background: #e0e0e0;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.1);
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #667eea, #764ba2);
  border-radius: 10px;
  transition: width 0.3s ease;
}

.question-container {
  margin-bottom: 30px;
  text-align: center;
}

.question-number {
  font-size: 20px;
  font-weight: 700;
  color: #FF5722;
  margin-bottom: 20px;
}

.question-text {
  font-size: 24px;
  font-weight: 600;
  color: #2c3e50;
  line-height: 1.6;
}

.options-container {
  display: flex;
  flex-direction: column;
  gap: 15px;
  margin-bottom: 20px;
}

.option-button {
  display: flex;
  align-items: center;
  gap: 15px;
  padding: 20px;
  background: #f8f9ff;
  border: 3px solid #667eea;
  border-radius: 16px;
  font-size: 18px;
  font-weight: 600;
  color: #2c3e50;
  cursor: pointer;
  transition: all 0.3s ease;
  text-align: left;
}

.option-button:hover:not(:disabled) {
  background: #e8eaff;
  transform: translateX(5px);
  box-shadow: 0 4px 12px rgba(102, 126, 234, 0.3);
}

.option-button.selected {
  background: #667eea;
  color: white;
  border-color: #764ba2;
}

.option-button.correct {
  background: #4CAF50;
  color: white;
  border-color: #2E7D32;
}

.option-button.incorrect {
  background: #F44336;
  color: white;
  border-color: #C62828;
}

.option-button:disabled {
  cursor: not-allowed;
}

.option-letter {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 40px;
  height: 40px;
  background: rgba(255, 255, 255, 0.2);
  border-radius: 50%;
  font-weight: 700;
  flex-shrink: 0;
}

.option-text {
  flex: 1;
}

.result-icon {
  font-size: 24px;
  margin-left: auto;
}

.next-container {
  display: flex;
  justify-content: center;
  margin-top: 30px;
}

.next-button {
  padding: 16px 40px;
  background: linear-gradient(135deg, #4CAF50, #45a049);
  color: white;
  border: 3px solid #2E7D32;
  border-radius: 16px;
  font-size: 18px;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 12px rgba(76, 175, 80, 0.3);
}

.next-button:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(76, 175, 80, 0.5);
}

/* Results Screen */
.results-overlay {
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
  z-index: 1000;
  animation: fade-in 0.3s ease;
  padding: 20px;
}

.results-modal {
  background: linear-gradient(135deg, #667eea, #764ba2);
  border-radius: 30px;
  max-width: 600px;
  width: 100%;
  position: relative;
  box-shadow: 
    0 30px 80px rgba(0, 0, 0, 0.4),
    0 0 0 4px #FFD700,
    0 0 0 8px #FF9800;
  animation: modal-zoom 0.4s ease;
}

@keyframes modal-zoom {
  from {
    opacity: 0;
    transform: scale(0.8);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

.close-results-btn {
  position: absolute;
  top: 15px;
  right: 15px;
  background: #F44336;
  color: white;
  border: 3px solid #FFD54F;
  width: 45px;
  height: 45px;
  border-radius: 50%;
  cursor: pointer;
  font-size: 24px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
  box-shadow: 0 4px 12px rgba(244, 67, 54, 0.4);
}

.close-results-btn:hover {
  background: #D32F2F;
  transform: rotate(90deg) scale(1.1);
}

.results-content {
  background: white;
  border-radius: 25px;
  padding: 50px 40px;
  text-align: center;
  margin: 10px;
}

.results-icon {
  font-size: 100px;
  margin-bottom: 20px;
  animation: bounce 2s ease-in-out infinite;
}

.results-title {
  font-size: 36px;
  font-weight: 900;
  color: #667eea;
  margin-bottom: 10px;
}

.results-player {
  font-size: 24px;
  font-weight: 700;
  color: #764ba2;
  margin-bottom: 30px;
}

.score-display {
  margin-bottom: 20px;
}

.score-value {
  font-size: 80px;
  font-weight: 900;
  color: #FFD700;
  text-shadow: 3px 3px 0px #FF9800, 6px 6px 0px rgba(0, 0, 0, 0.2);
  line-height: 1;
}

.score-label {
  font-size: 24px;
  color: #666;
  font-weight: 600;
  margin-top: 10px;
}

.score-percentage {
  font-size: 32px;
  font-weight: 700;
  color: #4CAF50;
  margin-bottom: 20px;
}

.score-message {
  font-size: 20px;
  color: #666;
  margin-bottom: 40px;
  font-weight: 600;
}

.results-actions {
  display: flex;
  gap: 15px;
  justify-content: center;
  flex-wrap: wrap;
}

.action-button {
  padding: 16px 32px;
  border-radius: 16px;
  font-size: 16px;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.3s ease;
  border: 3px solid;
}

.action-button.primary {
  background: linear-gradient(135deg, #FFD700, #FFA500);
  color: #2c3e50;
  border-color: #FF5722;
  box-shadow: 0 4px 12px rgba(255, 215, 0, 0.4);
}

.action-button.primary:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(255, 215, 0, 0.6);
}

.action-button.secondary {
  background: #667eea;
  color: white;
  border-color: #764ba2;
  box-shadow: 0 4px 12px rgba(102, 126, 234, 0.4);
}

.action-button.secondary:hover {
  background: #764ba2;
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(118, 75, 162, 0.6);
}

.action-button.danger {
  background: #F44336;
  color: white;
  border-color: #C62828;
  box-shadow: 0 4px 12px rgba(244, 67, 54, 0.4);
}

.action-button.danger:hover {
  background: #D32F2F;
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(244, 67, 54, 0.6);
}

/* Leaderboard Screen */
.leaderboard-screen {
  animation: fade-in 0.5s ease;
}

.leaderboard-card {
  background: rgba(255, 255, 255, 0.95);
  border-radius: 30px;
  padding: 40px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
  border: 4px solid #FFD700;
}

.leaderboard-header {
  text-align: center;
  margin-bottom: 30px;
}

.leaderboard-title {
  font-size: 36px;
  font-weight: 900;
  color: #667eea;
  margin-bottom: 10px;
}

.leaderboard-subtitle {
  font-size: 18px;
  color: #666;
  font-weight: 600;
}

.leaderboard-list {
  display: flex;
  flex-direction: column;
  gap: 15px;
  margin-bottom: 30px;
  max-height: 500px;
  overflow-y: auto;
}

.leaderboard-entry {
  display: flex;
  align-items: center;
  gap: 20px;
  padding: 20px;
  background: #f8f9ff;
  border: 3px solid #e0e0e0;
  border-radius: 16px;
  transition: all 0.3s ease;
}

.leaderboard-entry:hover {
  transform: translateX(5px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.leaderboard-entry.top-three {
  background: linear-gradient(135deg, #FFF9E6, #FFE4B5);
  border-color: #FFD700;
}

.leaderboard-entry.current-player {
  border-color: #4CAF50;
  border-width: 4px;
  background: #E8F5E9;
}

.rank {
  font-size: 24px;
  font-weight: 700;
  min-width: 50px;
  text-align: center;
}

.medal {
  font-size: 32px;
}

.rank-number {
  color: #667eea;
}

.player-info {
  flex: 1;
  text-align: left;
}

.player-name {
  font-size: 20px;
  font-weight: 700;
  color: #2c3e50;
  margin-bottom: 5px;
}

.player-date {
  font-size: 14px;
  color: #999;
}

.player-score {
  text-align: right;
}

.score-points {
  font-size: 24px;
  font-weight: 900;
  color: #667eea;
}

.score-percent {
  font-size: 14px;
  color: #999;
  font-weight: 600;
}

.empty-leaderboard {
  text-align: center;
  padding: 60px 20px;
  color: #999;
}

.empty-icon {
  font-size: 80px;
  margin-bottom: 20px;
  animation: bounce 2s ease-in-out infinite;
}

.leaderboard-actions {
  display: flex;
  gap: 15px;
  justify-content: center;
  flex-wrap: wrap;
}

@keyframes fade-in {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Desktop Optimizations */
@media (min-width: 1025px) {
  .quiz-card-page {
    padding: 60px 40px;
  }

  .back-button {
    top: 30px;
    left: 30px;
    padding: 14px 28px;
    font-size: 16px;
  }

  .header {
    position: absolute;
    top: 20px;
    left: 50%;
    transform: translateX(-50%);
    margin: 0;
  }

  .header h1 {
    font-size: 48px;
  }

  .subtitle {
    font-size: 18px;
  }

  .quiz-content {
    max-width: 900px;
    margin-top: 120px;
  }

  .entry-card {
    padding: 50px;
    max-height: none;
    overflow: visible;
  }

  .quiz-card {
    padding: 32px;
    max-height: none;
    overflow: visible;
  }

  .progress-container {
    margin-bottom: 16px;
  }

  .question-container {
    margin-bottom: 16px;
  }

  .question-text {
    font-size: 22px;
  }

  .options-container {
    gap: 12px;
    margin-bottom: 12px;
  }

  .option-button {
    padding: 16px;
    font-size: 16px;
  }

  .option-letter {
    width: 36px;
    height: 36px;
  }

  .next-container {
    margin-top: 20px;
  }

  .leaderboard-card {
    padding: 50px;
  }

  .decoration {
    font-size: 80px;
  }

  .name-entry-screen {
    transform: translateY(-20px);
  }
}

/* Responsive Design */
@media (max-width: 768px) {
  .quiz-card-page {
    padding: 20px 15px;
  }

  .back-button {
    top: 15px;
    left: 15px;
    padding: 10px 16px;
    font-size: 14px;
    border-radius: 20px;
  }

  .header {
    margin-top: 50px;
    margin-bottom: 30px;
  }

  .header h1 {
    font-size: 32px;
  }

  .subtitle {
    font-size: 16px;
  }

  .entry-card,
  .quiz-card,
  .leaderboard-card {
    padding: 30px 20px;
  }

  .quiz-card {
    max-height: 80vh;
  }

  .entry-title {
    font-size: 28px;
  }

  .entry-description {
    font-size: 16px;
  }

  .question-text {
    font-size: 20px;
  }

  .option-button {
    padding: 15px;
    font-size: 16px;
  }

  .results-content {
    padding: 40px 30px;
  }

  .results-title {
    font-size: 28px;
  }

  .score-value {
    font-size: 60px;
  }

  .leaderboard-entry {
    padding: 15px;
    gap: 15px;
  }

  .player-name {
    font-size: 18px;
  }

  .results-actions,
  .leaderboard-actions {
    flex-direction: column;
  }

  .action-button {
    width: 100%;
  }
}

@media (max-width: 480px) {
  .back-button {
    top: 10px;
    left: 10px;
    padding: 8px 12px;
    font-size: 12px;
    border-radius: 15px;
  }

  .header {
    margin-top: 45px;
    margin-bottom: 25px;
  }

  .header h1 {
    font-size: 24px;
  }

  .entry-card,
  .quiz-card,
  .leaderboard-card {
    padding: 25px 15px;
  }

  .quiz-card {
    max-height: 75vh;
  }

  .entry-icon {
    font-size: 60px;
  }

  .entry-title {
    font-size: 24px;
  }

  .question-text {
    font-size: 18px;
  }

  .option-button {
    padding: 12px;
    font-size: 14px;
  }

  .option-letter {
    width: 32px;
    height: 32px;
    font-size: 14px;
  }

  .results-icon {
    font-size: 70px;
  }

  .score-value {
    font-size: 50px;
  }

  .decoration {
    font-size: 40px;
  }
}
</style>

