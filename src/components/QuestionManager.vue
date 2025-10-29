<template>
  <div class="manager-wrapper">
    <!-- Floating Door Decorations -->
    <div class="floating-doors">
      <div class="door-decoration door-1">🚪</div>
      <div class="door-decoration door-2">🚪</div>
      <div class="door-decoration door-3">🚪</div>
      <div class="door-decoration door-4">🚪</div>
      <div class="door-decoration door-5">🚪</div>
          </div>

    <!-- Back to Game Button -->
    <button class="back-button" @click="goToGame">
      ← Back to Game
    </button>

    <!-- Header -->
    <header class="manager-header">
      <div class="header-content">
        <div class="title-section">
          <div class="logo-icon">🚪</div>
          <h1>DOOR QUEST Manager</h1>
        </div>
        <p class="subtitle">Manage your questions and create amazing learning adventures!</p>
      </div>
    </header>

    <!-- Navigation Tabs -->
    <nav class="tab-navigation">
        <button
          v-for="item in navItems"
        :key="item.key"
          @click="setSection(item.key)"
        class="tab-button"
        :class="{ 'active': activeSection === item.key }"
        >
        <span class="tab-icon">{{ item.icon }}</span>
        <span class="tab-label">{{ item.label }}</span>
        </button>
      </nav>

    <!-- Main Content -->
    <main class="manager-content">
        <!-- Statistics -->
      <section v-if="activeSection === 'statistics'" class="section-container">
        <h2 class="section-title">📊 Statistics Overview</h2>
        <div class="stats-grid" :key="localQuestions.length">
          <div
            v-for="(stat, index) in stats"
              :key="`${stat.label}-${localQuestions.length}`"
            class="stat-card"
            :style="{ animationDelay: `${index * 0.1}s` }"
          >
            <div class="stat-value">{{ stat.value }}</div>
            <div class="stat-label">{{ stat.label }}</div>
            <div class="stat-icon">{{ ['🎯', '✨', '✅', '📚'][index] }}</div>
            </div>
          </div>
        </section>

        <!-- Add New -->
      <section v-if="activeSection === 'add'" class="section-container">
        <h2 class="section-title">➕ Add New Question</h2>
        <form @submit.prevent="saveQuestion" class="question-form">
          <div class="form-group">
            <label class="form-label">
              <span class="label-icon">❓</span>
              Question
            </label>
              <textarea
                v-model="form.question"
              placeholder="Enter your question here..."
                rows="4"
              class="form-textarea"
                required
              ></textarea>
            </div>
          <div class="form-group">
            <label class="form-label">
              <span class="label-icon">✔️</span>
              Answer
            </label>
              <textarea
                v-model="form.answer"
                placeholder="Enter the correct answer..."
                rows="3"
              class="form-textarea"
                required
              ></textarea>
            </div>
          <button type="submit" class="save-button">
            <span class="btn-icon">💾</span>
            <span class="btn-text">Save Question</span>
            <div class="btn-shine"></div>
            </button>
          </form>
        </section>

        <!-- Questions -->
      <section v-if="activeSection === 'questions'" class="section-container">
        <h2 class="section-title">📋 Questions List</h2>
        <div v-if="localQuestions.length" class="questions-grid">
          <div
            v-for="(question, index) in localQuestions"
            :key="question.id"
            class="question-card"
            :style="{ animationDelay: `${index * 0.05}s` }"
          >
            <div class="question-number">{{ question.id }}</div>
            <div class="question-body">
              <h3 class="question-text">{{ question.question }}</h3>
              <div class="answer-box">
                <span class="answer-label">Answer:</span>
                <p class="answer-text">{{ question.answer }}</p>
              </div>
            </div>
            <div class="question-actions">
              <button @click="editQuestion(index)" class="action-btn edit-btn">
                ✏️ Edit
                  </button>
              <button @click="deleteQuestion(index)" class="action-btn delete-btn">
                🗑️ Delete
                  </button>
                </div>
              </div>
            </div>
        <div v-else class="empty-state">
          <div class="empty-icon">📭</div>
          <p>No questions yet! Click "Add New" to create your first question!</p>
          </div>
        </section>

        <!-- Play Game -->
      <section v-if="activeSection === 'playgame'" class="section-container play-section">
        <div class="play-content">
          <div class="play-icon">🎮</div>
          <h2 class="play-title">Ready to Play?</h2>
          <p class="play-description">
            Test your questions and embark on an exciting Door Quest adventure!
          </p>
          <button @click="goToGame" class="play-button">
            <span class="btn-icon">🚀</span>
            <span class="btn-text">Launch Game</span>
            <div class="btn-shine"></div>
          </button>
        </div>
        </section>

        <!-- Quiz Scores -->
      <section v-if="activeSection === 'quizscores'" class="section-container">
        <h2 class="section-title">🏆 Quiz Card Scores Management</h2>
        <div class="quiz-scores-content">
          <div class="info-box">
            <div class="info-icon">ℹ️</div>
            <div class="info-text">
              <h3>Manage Quiz Card Leaderboard</h3>
              <p>This section allows you to clear all Quiz Card scores and reset the leaderboard. This action cannot be undone.</p>
            </div>
          </div>

          <div class="stats-display">
            <div class="stat-item">
              <div class="stat-icon-large">📝</div>
              <div class="stat-content">
                <div class="stat-number">{{ quizScoresCount }}</div>
                <div class="stat-text">Total Scores</div>
              </div>
            </div>
          </div>

          <div class="danger-zone">
            <h3 class="danger-title">⚠️ Danger Zone</h3>
            <p class="danger-description">
              Clearing all scores will permanently delete all Quiz Card leaderboard entries. Students who have taken the quiz will be able to retake it.
            </p>
            <button @click="clearQuizScores" class="danger-button">
              <span class="btn-icon">🗑️</span>
              <span class="btn-text">Clear All Quiz Scores</span>
            </button>
          </div>
        </div>
      </section>
      </main>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'

const props = defineProps({
  questions: {
    type: Array,
    default: () => []
  }
})

const emit = defineEmits(['goToGame', 'updateQuestions'])

const activeSection = ref('statistics')
const form = ref({ question: '', answer: '' })
const localQuestions = ref([...props.questions])

// Watch for changes in props.questions and update localQuestions
watch(() => props.questions, (newQuestions) => {
  localQuestions.value = [...newQuestions]
}, { deep: true })

const navItems = [
  { label: 'Statistics', key: 'statistics', icon: '📊' },
  { label: 'Add New', key: 'add', icon: '➕' },
  { label: 'Questions', key: 'questions', icon: '📋' },
  { label: 'Play Game', key: 'playgame', icon: '🎮' },
  { label: 'Quiz Scores', key: 'quizscores', icon: '🏆' }
]

const stats = computed(() => [
  { label: 'Total Questions', value: localQuestions.value.length },
  { label: 'Total Characters', value: localQuestions.value.reduce((s, q) => s + (q.question?.length || 0) + (q.answer?.length || 0), 0) },
  { label: 'With Answers', value: localQuestions.value.filter(q => q.answer && q.answer.trim()).length },
  { label: 'Rows (3 per)', value: Math.ceil(localQuestions.value.length / 3) }
])

function setSection(key) {
  activeSection.value = key
}

function goToGame() {
  emit('updateQuestions', localQuestions.value)
  emit('goToGame', localQuestions.value)
}

function saveQuestion() {
  if (!form.value.question.trim() || !form.value.answer.trim()) return
  // Generate simple sequential ID
  const id = localQuestions.value.length > 0 
    ? Math.max(...localQuestions.value.map(q => q.id)) + 1 
    : 1
  const newQuestion = { id, question: form.value.question.trim(), answer: form.value.answer.trim() }
  localQuestions.value = [...localQuestions.value, newQuestion]
  form.value = { question: '', answer: '' }
  activeSection.value = 'questions'
}

function editQuestion(index) {
  form.value = { ...localQuestions.value[index] }
  localQuestions.value.splice(index, 1)
  activeSection.value = 'add'
}

function deleteQuestion(index) {
  if (confirm('Delete this question? 🗑️')) {
    localQuestions.value.splice(index, 1)
    // Re-number all questions sequentially after deletion
    renumberQuestions()
  }
}

function renumberQuestions() {
  localQuestions.value = localQuestions.value.map((question, index) => ({
    ...question,
    id: index + 1
  }))
}

// Quiz Scores Management
const quizScoresCount = computed(() => {
  try {
    const scores = JSON.parse(localStorage.getItem('quizCardScores') || '[]')
    return scores.length
  } catch (error) {
    return 0
  }
})

function clearQuizScores() {
  if (confirm('⚠️ Are you sure you want to clear ALL Quiz Card scores?\n\nThis will:\n• Delete all leaderboard entries\n• Allow students to retake the quiz\n• Cannot be undone\n\nProceed?')) {
    try {
      localStorage.removeItem('quizCardScores')
      alert('✅ All Quiz Card scores have been cleared successfully!')
    } catch (error) {
      console.error('Error clearing quiz scores:', error)
      alert('❌ Error clearing scores. Please try again.')
    }
  }
}
</script>

<style scoped>
.manager-wrapper {
  height: 100vh;
  background: linear-gradient(135deg, #ffecd2 0%, #fcb69f 50%, #a8edea 100%);
  padding: 20px;
  position: relative;
  overflow-x: hidden;
  overflow-y: auto;
  width: 100%;
  box-sizing: border-box;
}

.manager-wrapper::before {
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

.manager-wrapper::after {
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

/* Back Button */
.back-button {
  position: fixed;
  top: 20px;
  left: 20px;
  background: rgba(0, 0, 0, 0.8);
  color: white;
  border: 2px solid rgba(0, 0, 0, 0.9);
  padding: 12px 20px;
  border-radius: 12px;
  font-size: 16px;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.3s ease;
  z-index: 100;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
  backdrop-filter: blur(10px);
}

.back-button:hover {
  background: rgba(0, 0, 0, 0.9);
  transform: translateX(-5px) scale(1.05);
  box-shadow: 0 6px 16px rgba(0, 0, 0, 0.4);
}

/* Header */
.manager-header {
  text-align: center;
  margin-bottom: 30px;
  position: relative;
  z-index: 2;
}

.header-content {
  background: rgba(255, 255, 255, 0.15);
  padding: 30px 20px;
  border-radius: 30px;
  backdrop-filter: blur(10px);
  border: 2px solid rgba(255, 255, 255, 0.3);
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
}

.title-section {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 15px;
  margin-bottom: 15px;
}

.logo-icon {
  font-size: 48px;
  animation: doorBounce 2s ease-in-out infinite;
}

.manager-header h1 {
  font-size: 42px;
  font-weight: 900;
  color: white;
  text-shadow: 
    -1px -1px 0 #000,
    1px -1px 0 #000,
    -1px 1px 0 #000,
    1px 1px 0 #000,
    2px 2px 4px rgba(0, 0, 0, 0.3);
  margin: 0;
  letter-spacing: -1px;
}

.subtitle {
  color: white;
  font-size: 18px;
  font-weight: 600;
  text-shadow: 
    -1px -1px 0 #000,
    1px -1px 0 #000,
    -1px 1px 0 #000,
    1px 1px 0 #000,
    1px 1px 2px rgba(0, 0, 0, 0.3);
  margin: 0;
}

/* Tab Navigation */
.tab-navigation {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-bottom: 30px;
  flex-wrap: wrap;
  position: relative;
  z-index: 2;
}

.tab-button {
  background: white;
  border: 4px solid #FF9800;
  padding: 15px 25px;
  border-radius: 20px;
  font-size: 16px;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  gap: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.tab-button:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 20px rgba(255, 152, 0, 0.4);
  background: #FFF3E0;
}

.tab-button.active {
  background: #FF5722;
  border-color: #FFD54F;
  color: white;
  transform: scale(1.1);
  box-shadow: 0 8px 25px rgba(255, 87, 34, 0.5);
}

.tab-icon {
  font-size: 20px;
}

.tab-label {
  font-size: 15px;
}

/* Main Content */
.manager-content {
  max-width: 1200px;
  margin: 0 auto;
  position: relative;
  z-index: 2;
}

.section-container {
  background: rgba(255, 255, 255, 0.9);
  border-radius: 30px;
  padding: 40px;
  box-shadow: 0 15px 40px rgba(0, 0, 0, 0.2);
  border: 4px solid #2196F3;
  animation: section-fade-in 0.5s ease;
}

.section-title {
  font-size: 32px;
  font-weight: 900;
  color: #E91E63;
  text-shadow: 2px 2px 0px #FF9800, 4px 4px 0px #2196F3;
  margin-bottom: 30px;
  text-align: center;
}

/* Statistics */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 20px;
}

.stat-card {
  background: linear-gradient(135deg, #667eea, #764ba2);
  border: 4px solid #FFD54F;
  border-radius: 20px;
  padding: 30px;
  text-align: center;
  position: relative;
  overflow: hidden;
  animation: stat-fade-in 0.6s ease forwards;
  opacity: 0;
  box-shadow: 0 10px 25px rgba(102, 126, 234, 0.4);
  transition: all 0.3s ease;
}

.stat-card:hover {
  transform: translateY(-10px) scale(1.05);
  box-shadow: 0 15px 35px rgba(102, 126, 234, 0.6);
}

.stat-value {
  font-size: 48px;
  font-weight: 900;
  color: #FFD700;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
  margin-bottom: 10px;
}

.stat-label {
  font-size: 14px;
  text-transform: uppercase;
  font-weight: 700;
  color: white;
  letter-spacing: 1px;
}

.stat-icon {
  position: absolute;
  bottom: 10px;
  right: 10px;
  font-size: 40px;
  opacity: 0.3;
}

/* Form */
.question-form {
  max-width: 700px;
  margin: 0 auto;
}

.form-group {
  margin-bottom: 25px;
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

.form-textarea {
  width: 100%;
  padding: 15px 20px;
  border: 4px solid #FF9800;
  border-radius: 16px;
  font-size: 16px;
  font-family: inherit;
  resize: vertical;
  transition: all 0.3s ease;
  background: #FFF3E0;
  color: #333;
  box-shadow: inset 0 2px 4px rgba(0, 0, 0, 0.1);
}

.form-textarea:focus {
  outline: none;
  border-color: #FF5722;
  background: white;
  box-shadow: 0 0 0 4px rgba(255, 87, 34, 0.2);
}

.save-button,
.play-button {
  width: 100%;
  padding: 20px;
  background: linear-gradient(135deg, #FFD700, #FFA500);
  color: #2c3e50;
  border: 4px solid #FF5722;
  border-radius: 20px;
  font-size: 20px;
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

.save-button:hover,
.play-button:hover {
  transform: translateY(-5px) scale(1.05);
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

.save-button:hover .btn-shine,
.play-button:hover .btn-shine {
  left: 100%;
}

/* Questions Grid */
.questions-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
  gap: 20px;
  justify-items: center;
}

.question-card {
  background: #FFFFFF;
  border: 5px solid #4CAF50;
  border-radius: 20px;
  padding: 20px;
  position: relative;
  overflow: hidden;
  animation: card-fade-in 0.5s ease forwards;
  opacity: 0;
  transition: all 0.3s ease;
  box-shadow: 0 8px 20px rgba(76, 175, 80, 0.3);
  width: 100%;
  max-width: 320px;
  min-height: 280px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.question-card:hover {
  transform: translateY(-10px);
  box-shadow: 0 15px 35px rgba(76, 175, 80, 0.5);
  border-color: #FF5722;
}

.question-number {
  position: absolute;
  top: 10px;
  right: 10px;
  width: 40px;
  height: 40px;
  background: linear-gradient(135deg, #667eea, #764ba2);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 18px;
  font-weight: 900;
  color: #FFD700;
  box-shadow: 0 4px 10px rgba(102, 126, 234, 0.4);
}

.question-body {
  padding-right: 50px;
  flex: 1;
  display: flex;
  flex-direction: column;
}

.question-text {
  font-size: 16px;
  font-weight: 700;
  color: #2c3e50;
  margin-bottom: 15px;
  line-height: 1.5;
  flex: 1;
}

.answer-box {
  background: #E8F5E8;
  border-left: 4px solid #4CAF50;
  padding: 12px;
  border-radius: 8px;
  margin-bottom: 15px;
  flex-shrink: 0;
}

.answer-label {
  font-size: 12px;
  font-weight: 700;
  color: #2E7D32;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.answer-text {
  font-size: 14px;
  color: #1B5E20;
  margin: 5px 0 0 0;
  font-weight: 600;
}

.question-actions {
  display: flex;
  gap: 10px;
  margin-top: auto;
}

.action-btn {
  flex: 1;
  padding: 10px;
  border: 3px solid;
  border-radius: 12px;
  font-size: 14px;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.3s ease;
}

.edit-btn {
  background: #2196F3;
  border-color: #FFD54F;
  color: white;
}

.edit-btn:hover {
  background: #1976D2;
  transform: scale(1.05);
  box-shadow: 0 4px 12px rgba(33, 150, 243, 0.4);
}

.delete-btn {
  background: #F44336;
  border-color: #FFD54F;
  color: white;
}

.delete-btn:hover {
  background: #D32F2F;
  transform: scale(1.05);
  box-shadow: 0 4px 12px rgba(244, 67, 54, 0.4);
}

/* Empty State */
.empty-state {
  text-align: center;
  padding: 60px 20px;
  color: #666;
  font-size: 18px;
  font-weight: 600;
}

.empty-icon {
  font-size: 80px;
  margin-bottom: 20px;
  animation: empty-bounce 2s ease-in-out infinite;
}

/* Play Section */
.play-section {
  text-align: center;
}

.play-content {
  max-width: 600px;
  margin: 0 auto;
}

.play-icon {
  font-size: 100px;
  margin-bottom: 20px;
  animation: doorBounce 2s ease-in-out infinite;
}

.play-title {
  font-size: 36px;
  font-weight: 900;
  color: #4CAF50;
  text-shadow: 2px 2px 0px #FF9800, 4px 4px 0px #2196F3;
  margin-bottom: 15px;
}

.play-description {
  font-size: 18px;
  color: #555;
  font-weight: 600;
  margin-bottom: 30px;
}

/* Quiz Scores Section */
.quiz-scores-content {
  max-width: 800px;
  margin: 0 auto;
}

.info-box {
  display: flex;
  align-items: flex-start;
  gap: 20px;
  padding: 25px;
  background: linear-gradient(135deg, #E3F2FD, #BBDEFB);
  border-left: 5px solid #2196F3;
  border-radius: 16px;
  margin-bottom: 30px;
  box-shadow: 0 4px 12px rgba(33, 150, 243, 0.2);
}

.info-icon {
  font-size: 40px;
  flex-shrink: 0;
}

.info-text h3 {
  font-size: 20px;
  font-weight: 700;
  color: #1976D2;
  margin: 0 0 10px 0;
}

.info-text p {
  font-size: 16px;
  color: #555;
  margin: 0;
  line-height: 1.6;
}

.stats-display {
  display: flex;
  justify-content: center;
  margin-bottom: 30px;
}

.stat-item {
  display: flex;
  align-items: center;
  gap: 20px;
  padding: 30px 40px;
  background: linear-gradient(135deg, #FFF9E6, #FFE4B5);
  border: 4px solid #FFD700;
  border-radius: 20px;
  box-shadow: 0 8px 20px rgba(255, 215, 0, 0.3);
}

.stat-icon-large {
  font-size: 60px;
}

.stat-content {
  text-align: left;
}

.stat-number {
  font-size: 48px;
  font-weight: 900;
  color: #FF9800;
  line-height: 1;
  margin-bottom: 5px;
}

.stat-text {
  font-size: 16px;
  color: #666;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.danger-zone {
  padding: 30px;
  background: linear-gradient(135deg, #FFEBEE, #FFCDD2);
  border: 4px solid #F44336;
  border-radius: 20px;
  text-align: center;
  box-shadow: 0 8px 20px rgba(244, 67, 54, 0.3);
}

.danger-title {
  font-size: 24px;
  font-weight: 900;
  color: #C62828;
  margin: 0 0 15px 0;
}

.danger-description {
  font-size: 16px;
  color: #666;
  margin: 0 0 25px 0;
  line-height: 1.6;
}

.danger-button {
  padding: 18px 40px;
  background: linear-gradient(135deg, #F44336, #D32F2F);
  color: white;
  border: 4px solid #C62828;
  border-radius: 16px;
  font-size: 18px;
  font-weight: 800;
  cursor: pointer;
  transition: all 0.3s ease;
  text-transform: uppercase;
  letter-spacing: 1.5px;
  display: inline-flex;
  align-items: center;
  gap: 10px;
  box-shadow: 0 8px 20px rgba(244, 67, 54, 0.4);
}

.danger-button:hover {
  background: linear-gradient(135deg, #D32F2F, #B71C1C);
  transform: translateY(-3px);
  box-shadow: 0 12px 30px rgba(244, 67, 54, 0.6);
}

.danger-button:active {
  transform: translateY(-1px);
}

/* Animations */
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

@keyframes doorBounce {
  0%, 100% { transform: translateY(0) scale(1); }
  50% { transform: translateY(-15px) scale(1.1); }
}

@keyframes section-fade-in {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes stat-fade-in {
  from {
    opacity: 0;
    transform: translateY(20px) scale(0.9);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

@keyframes card-fade-in {
  from {
    opacity: 0;
    transform: translateY(15px) scale(0.95);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

@keyframes empty-bounce {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-10px); }
}

/* Responsive Styles */
@media (max-width: 768px) {
  .manager-wrapper {
    padding: 15px;
  }
  
  .back-button {
    top: 10px;
    left: 10px;
    padding: 10px 15px;
    font-size: 14px;
  }
  
  .manager-header h1 {
    font-size: 28px;
  }
  
  .subtitle {
    font-size: 14px;
  }
  
  .title-section {
    flex-direction: column;
    gap: 10px;
  }
  
  .logo-icon {
    font-size: 36px;
  }
  
  .tab-navigation {
    flex-direction: column;
    gap: 10px;
  }
  
  .tab-button {
    width: 100%;
    justify-content: center;
  }
  
  .section-container {
    padding: 25px 15px;
  }
  
  .section-title {
    font-size: 24px;
  }
  
  .stats-grid {
    grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  }
  
  .stat-value {
    font-size: 36px;
  }
  
  .questions-grid {
    grid-template-columns: 1fr;
    justify-items: center;
  }
  
  .question-card {
    max-width: 100%;
    width: 100%;
  }
  
  .play-icon {
    font-size: 70px;
  }
  
  .play-title {
    font-size: 28px;
  }
}

@media (max-width: 480px) {
  .manager-header h1 {
    font-size: 24px;
  }
  
  .section-title {
    font-size: 20px;
  }
  
  .stat-card {
    padding: 20px;
  }
  
  .stat-value {
    font-size: 28px;
  }
  
  .form-label {
    font-size: 16px;
  }
  
  .form-textarea {
    font-size: 14px;
    padding: 12px 15px;
  }
  
  .save-button,
  .play-button {
    font-size: 16px;
    padding: 15px;
  }
}
</style>
