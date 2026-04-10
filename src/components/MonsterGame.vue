<script setup>
import { ref, onMounted } from 'vue';
import GameArena from './GameArena.vue';
import CharacterCard from './CharacterCard.vue';
import MathProblem from './MathProblem.vue';

// Assets
import heroImage from '../assets/hero_kid.png';
import monsterImage from '../assets/goofy_monster.png';

const emit = defineEmits(['back']);

// State
const maxHealth = 100;
const heroCurrentHealth = ref(maxHealth);
const monsterCurrentHealth = ref(maxHealth);

const heroAnimState = ref('idle'); 
const monsterAnimState = ref('idle');

const questionText = ref('');
const choices = ref([]);
const correctAnswer = ref(0);
const selectedAnswer = ref(null);
const isAnswering = ref(false); 

const gameState = ref('playing'); 

// Methods
function generateRandomNumber(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}

function generateQuestion() {
  const num1 = generateRandomNumber(1, 10);
  const num2 = generateRandomNumber(1, 10);
  
  correctAnswer.value = num1 * num2;
  questionText.value = `${num1} × ${num2} = ?`;
  
  const wrongChoices = new Set();
  while (wrongChoices.size < 2) {
    let offset = generateRandomNumber(-10, 10);
    if (offset !== 0 && (correctAnswer.value + offset) > 0) {
       wrongChoices.add(correctAnswer.value + offset);
    }
  }

  const allChoices = [correctAnswer.value, ...Array.from(wrongChoices)];
  for (let i = allChoices.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [allChoices[i], allChoices[j]] = [allChoices[j], allChoices[i]];
  }
  
  choices.value = allChoices;
  selectedAnswer.value = null;
}

async function handleAnswerSelection(answer) {
  if (isAnswering.value) return;
  
  isAnswering.value = true;
  selectedAnswer.value = answer;

  if (answer === correctAnswer.value) {
    heroAnimState.value = 'attack';
    setTimeout(() => {
      monsterCurrentHealth.value = Math.max(0, monsterCurrentHealth.value - 20);
      monsterAnimState.value = 'hurt';
      
      setTimeout(() => {
        heroAnimState.value = 'idle';
        monsterAnimState.value = 'idle';
        checkWinCondition();
      }, 700);
      
    }, 400);

  } else {
    monsterAnimState.value = 'attack';
    setTimeout(() => {
      heroCurrentHealth.value = Math.max(0, heroCurrentHealth.value - 20);
      heroAnimState.value = 'hurt';
      
      setTimeout(() => {
        monsterAnimState.value = 'idle';
        heroAnimState.value = 'idle';
        checkWinCondition();
      }, 700);
      
    }, 400); 
  }
}

function checkWinCondition() {
  if (monsterCurrentHealth.value <= 0) {
    gameState.value = 'victory';
    monsterAnimState.value = 'defeat';
  } else if (heroCurrentHealth.value <= 0) {
    gameState.value = 'gameover';
    heroAnimState.value = 'defeat';
  } else {
    generateQuestion();
    isAnswering.value = false;
  }
}

function restartGame() {
  heroCurrentHealth.value = maxHealth;
  monsterCurrentHealth.value = maxHealth;
  heroAnimState.value = 'idle';
  monsterAnimState.value = 'idle';
  gameState.value = 'playing';
  generateQuestion();
  isAnswering.value = false;
}

onMounted(() => {
  generateQuestion();
});
</script>

<template>
  <GameArena>
    <!-- Back Button -->
    <button class="btn-back" @click="emit('back')">← Kembali ke Menu</button>
    
    <div class="header glass-panel">
      <h1>Pahlawan vs Monster</h1>
      <p>Jawab perkalian dengan benar untuk menyerang!</p>
    </div>

    <div class="battlefield">
      <CharacterCard 
        name="Pahlawan" 
        :image="heroImage" 
        :maxHealth="maxHealth" 
        :currentHealth="heroCurrentHealth"
        :animState="heroAnimState"
        :isMonster="false"
      />
      
      <CharacterCard 
        name="Monster Lendir" 
        :image="monsterImage" 
        :maxHealth="maxHealth" 
        :currentHealth="monsterCurrentHealth"
        :animState="monsterAnimState"
        :isMonster="true"
      />
    </div>

    <div class="controls-area">
      <MathProblem 
        v-if="gameState === 'playing'"
        :questionText="questionText"
        :choices="choices"
        :disabled="isAnswering"
        :selectedAnswer="selectedAnswer"
        :correctAnswer="correctAnswer"
        @answer="handleAnswerSelection"
      />

      <div v-if="gameState === 'victory'" class="result-box glass-panel victory">
        <h1>🎉 MENANG! 🎉</h1>
        <p>Kamu berhasil mengalahkan monster matematika!</p>
        <button class="btn-choice correct" @click="restartGame">Main Lagi</button>
      </div>

      <div v-if="gameState === 'gameover'" class="result-box glass-panel defeat">
        <h1>😢 KALAH 😢</h1>
        <p>Pahlawan kehabisan energi. Jangan menyerah, ayo coba lagi!</p>
        <button class="btn-choice correct" @click="restartGame">Main Lagi</button>
      </div>
    </div>
  </GameArena>
</template>

<style scoped>
.btn-back {
  position: absolute;
  top: 20px;
  left: 20px;
  z-index: 100;
  background: white;
  border: none;
  padding: 10px 20px;
  border-radius: 20px;
  font-family: 'Fredoka', sans-serif;
  font-weight: bold;
  cursor: pointer;
  box-shadow: 0 4px 6px rgba(0,0,0,0.1);
  transition: transform 0.2s;
}
.btn-back:hover {
  transform: translateY(-2px);
}

.header {
  margin: 20px auto;
  padding: 10px 40px;
  text-align: center;
  max-width: 600px;
}

.header h1 {
  margin: 0;
  font-family: 'Fredoka', sans-serif;
  color: var(--primary);
  font-size: 2rem;
}

.header p {
  margin: 5px 0 0 0;
  font-weight: 500;
  color: var(--text-dark);
}

.battlefield {
  display: flex;
  justify-content: space-between;
  align-items: flex-end;
  padding: 0 5%;
  flex-grow: 1;
  margin-bottom: 2rem;
}

.controls-area {
  min-height: 250px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.result-box {
  text-align: center;
  padding: 40px 60px;
  margin-bottom: 30px;
}

.result-box h1 {
  font-size: 3rem;
  margin-top: 0;
  font-family: 'Fredoka', sans-serif;
}

.result-box p {
  font-size: 1.5rem;
  margin-bottom: 30px;
  color: var(--text-dark);
}

.result-box.victory h1 {
  color: #4CAF50;
}

.result-box.defeat h1 {
  color: #F44336;
}
</style>
