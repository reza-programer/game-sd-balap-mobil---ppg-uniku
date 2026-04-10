<script setup>
import { ref, onMounted, computed } from 'vue';

const emit = defineEmits(['back']);

// State
const tugPosition = ref(0); // -50 (Kiri/Merah Menang) to +50 (Kanan/Biru Menang)
const maxTug = 50;

const questionText = ref('');
const choices = ref([]);
const correctAnswer = ref(0);

const redStunned = ref(false);
const blueStunned = ref(false);

const gameState = ref('playing'); // 'playing', 'red_wins', 'blue_wins'

// Methods
function generateRandomNumber(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}

function generateQuestion() {
  const num1 = generateRandomNumber(1, 10);
  const num2 = generateRandomNumber(1, 10);
  
  correctAnswer.value = num1 * num2;
  questionText.value = `${num1} × ${num2}`;
  
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
}

function answerRed(choice) {
  if (gameState.value !== 'playing' || redStunned.value) return;
  
  if (choice === correctAnswer.value) {
    // Red wins the tug!
    tugPosition.value -= 10;
    generateQuestion(); // New question immediately because someone got it right
    checkWinCondition();
  } else {
    // Red gets a wrong answer penalty
    tugPosition.value += 5; // Slipping towards Blue
    redStunned.value = true;
    setTimeout(() => { redStunned.value = false; }, 1500);
    checkWinCondition();
  }
}

function answerBlue(choice) {
  if (gameState.value !== 'playing' || blueStunned.value) return;
  
  if (choice === correctAnswer.value) {
    // Blue wins the tug!
    tugPosition.value += 10;
    generateQuestion();
    checkWinCondition();
  } else {
    // Blue gets a wrong answer penalty
    tugPosition.value -= 5;
    blueStunned.value = true;
    setTimeout(() => { blueStunned.value = false; }, 1500);
    checkWinCondition();
  }
}

function checkWinCondition() {
  if (tugPosition.value <= -maxTug) {
    gameState.value = 'red_wins';
  } else if (tugPosition.value >= maxTug) {
    gameState.value = 'blue_wins';
  }
}

function restartGame() {
  tugPosition.value = 0;
  redStunned.value = false;
  blueStunned.value = false;
  gameState.value = 'playing';
  generateQuestion();
}

onMounted(() => {
  generateQuestion();
});

// Computed: only the FLAG slides
const flagStyle = computed(() => {
  // tugPosition: -50 (red wins, flag far left) to +50 (blue wins, flag far right)
  // Map to percentage of rope-container: center is 50%, range ±30%
  const pct = 50 + (tugPosition.value / maxTug) * 30;
  return {
    left: `${pct}%`
  };
});

</script>

<template>
  <div class="tug-arena">
    <!-- Back Button -->
    <button class="btn-back" @click="emit('back')">← Kembali ke Menu</button>
    <div class="vignette"></div>

    <div class="game-content">
      
      <!-- Central Question display -->
      <div v-if="gameState === 'playing'" class="question-board glass-panel-dark">
        <h2>Siapa Cepat Dia Dapat!</h2>
        <h1 class="bounce">{{ questionText }}</h1>
      </div>

      <!-- Result Screen -->
      <div v-else class="result-board glass-panel-dark">
        <h1 :class="{'red-text': gameState === 'red_wins', 'blue-text': gameState === 'blue_wins'}">
          TIM {{ gameState === 'red_wins' ? 'MERAH' : 'BIRU' }} MENANG!
        </h1>
        <button class="btn-choice" @click="restartGame">Main Lagi</button>
      </div>

      <!-- The Rope and Characters arena -->
      <div class="rope-container">

        <!-- WIN LINES -->
        <div class="win-line left-line"><span class="win-label">🏆</span></div>
        <div class="win-line right-line"><span class="win-label">🏆</span></div>
        <div class="center-line"></div>

        <!-- BRAIDED ROPE spans the full width -->
        <div class="rope-full">
          <div class="rope-strand strand-1"></div>
          <div class="rope-strand strand-2"></div>
          <div class="rope-strand strand-3"></div>
        </div>

        <!-- SLIDING FLAG: Indonesian flag (Merah-Putih) -->
        <div class="rope-flag" :style="flagStyle">
          <div class="flag-red"></div>
          <div class="flag-white"></div>
          <div class="flag-pole"></div>
        </div>

        <!-- Hand-rope connectors -->
        <div class="hand-rope red-hand-rope"></div>
        <div class="hand-rope blue-hand-rope"></div>

        <!-- TEAM RED: fixed left position, 5 people -->
        <div class="team red-team">
          <svg v-for="i in 5" :key="'r'+i" class="person-svg red-person" viewBox="0 0 60 110" xmlns="http://www.w3.org/2000/svg">
            <!-- Head -->
            <circle cx="42" cy="14" r="11" fill="#FFCC80" stroke="#E65100" stroke-width="2"/>
            <ellipse cx="42" cy="6" rx="11" ry="7" fill="#5D4037"/>
            <!-- Smile -->
            <path d="M37 17 Q42 22 47 17" stroke="#E65100" stroke-width="1.5" fill="none" stroke-linecap="round"/>
            <circle cx="38" cy="12" r="1.5" fill="#333"/>
            <circle cx="46" cy="12" r="1.5" fill="#333"/>
            <!-- Body leaning back (angled torso) -->
            <rect x="30" y="25" width="20" height="26" rx="5" fill="#E53935" stroke="#B71C1C" stroke-width="1.5" transform="rotate(-10 40 38)"/>
            <!-- Arms stretching LEFT toward rope -->
            <line x1="30" y1="31" x2="6" y2="44" stroke="#FFCC80" stroke-width="5" stroke-linecap="round"/>
            <line x1="30" y1="38" x2="6" y2="49" stroke="#FFCC80" stroke-width="5" stroke-linecap="round"/>
            <!-- Hands on rope -->
            <circle cx="6" cy="44" r="5" fill="#FFCC80" stroke="#E65100" stroke-width="1"/>
            <circle cx="6" cy="49" r="5" fill="#FFCC80" stroke="#E65100" stroke-width="1"/>
            <!-- Legs planted -->
            <line x1="37" y1="51" x2="25" y2="78" stroke="#1565C0" stroke-width="8" stroke-linecap="round"/>
            <line x1="43" y1="51" x2="53" y2="78" stroke="#1565C0" stroke-width="8" stroke-linecap="round"/>
            <ellipse cx="23" cy="80" rx="9" ry="5" fill="#222"/>
            <ellipse cx="55" cy="80" rx="9" ry="5" fill="#222"/>
          </svg>
        </div>

        <!-- TEAM BLUE: fixed right position, 5 people (mirror of red) -->
        <div class="team blue-team">
          <svg v-for="i in 5" :key="'b'+i" class="person-svg blue-person" viewBox="0 0 60 110" xmlns="http://www.w3.org/2000/svg">
            <!-- Head (mirrored: head on LEFT side) -->
            <circle cx="18" cy="14" r="11" fill="#FFCC80" stroke="#0D47A1" stroke-width="2"/>
            <ellipse cx="18" cy="6" rx="11" ry="7" fill="#212121"/>
            <!-- Smile -->
            <path d="M13 17 Q18 22 23 17" stroke="#0D47A1" stroke-width="1.5" fill="none" stroke-linecap="round"/>
            <circle cx="14" cy="12" r="1.5" fill="#333"/>
            <circle cx="22" cy="12" r="1.5" fill="#333"/>
            <!-- Body leaning back to the right -->
            <rect x="10" y="25" width="20" height="26" rx="5" fill="#1E88E5" stroke="#0D47A1" stroke-width="1.5" transform="rotate(10 20 38)"/>
            <!-- Arms stretching RIGHT toward rope -->
            <line x1="30" y1="31" x2="54" y2="44" stroke="#FFCC80" stroke-width="5" stroke-linecap="round"/>
            <line x1="30" y1="38" x2="54" y2="49" stroke="#FFCC80" stroke-width="5" stroke-linecap="round"/>
            <!-- Hands on rope -->
            <circle cx="54" cy="44" r="5" fill="#FFCC80" stroke="#0D47A1" stroke-width="1"/>
            <circle cx="54" cy="49" r="5" fill="#FFCC80" stroke="#0D47A1" stroke-width="1"/>
            <!-- Legs planted -->
            <line x1="17" y1="51" x2="7" y2="78" stroke="#880E4F" stroke-width="8" stroke-linecap="round"/>
            <line x1="23" y1="51" x2="35" y2="78" stroke="#880E4F" stroke-width="8" stroke-linecap="round"/>
            <ellipse cx="5" cy="80" rx="9" ry="5" fill="#222"/>
            <ellipse cx="37" cy="80" rx="9" ry="5" fill="#222"/>
          </svg>
        </div>

      </div>

      <!-- Controls Split Screen -->
      <div class="split-controls">
        
        <!-- RED TEAM CONTROLS -->
        <div class="control-panel red-panel">
          <div class="panel-header" :class="{'stunned': redStunned}">
            <h3>TIM MERAH</h3>
            <span v-if="redStunned" class="stun-warn">PILIHAN SALAH! (Tunggu)</span>
          </div>
          <div class="buttons-grid">
            <button 
              v-for="choice in choices" 
              :key="'red-'+choice"
              @click="answerRed(choice)"
              :disabled="redStunned || gameState !== 'playing'"
              class="btn-team btn-red"
            >
              {{ choice }}
            </button>
          </div>
        </div>

        <div class="divider">VS</div>

        <!-- BLUE TEAM CONTROLS -->
        <div class="control-panel blue-panel">
          <div class="panel-header" :class="{'stunned': blueStunned}">
            <h3>TIM BIRU</h3>
            <span v-if="blueStunned" class="stun-warn">PILIHAN SALAH! (Tunggu)</span>
          </div>
          <div class="buttons-grid">
            <button 
              v-for="choice in choices" 
              :key="'blue-'+choice"
              @click="answerBlue(choice)"
              :disabled="blueStunned || gameState !== 'playing'"
              class="btn-team btn-blue"
            >
              {{ choice }}
            </button>
          </div>
        </div>

      </div>

    </div>
  </div>
</template>

<style scoped>
.tug-arena {
  width: 100vw;
  height: 100vh;
  position: relative;
  background-image: url('../assets/tug_arena.png');
  background-size: cover;
  background-position: center bottom;
  display: flex;
  flex-direction: column;
}

.vignette {
  position: absolute;
  top: 0; left: 0; width: 100%; height: 100%;
  background: radial-gradient(circle, rgba(0,0,0,0) 30%, rgba(0,0,0,0.6) 100%);
  pointer-events: none;
}

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

.game-content {
  flex-grow: 1;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  z-index: 10;
  position: relative;
}

.question-board {
  position: absolute;
  top: 40px;
  left: 50%;
  transform: translateX(-50%);
  text-align: center;
  padding: 20px 60px;
}

.question-board h2 { margin: 0; color: #FFF176; font-family: 'Fredoka', sans-serif; }
.question-board h1 { font-size: 5rem; margin: 0; color: white; font-family: 'Fredoka', cursive; }

.bounce {
  animation: bounceScale 1s ease-in-out infinite alternate;
}
@keyframes bounceScale {
  0% { transform: scale(1); }
  100% { transform: scale(1.1); }
}

.result-board {
  position: absolute;
  top: 150px;
  left: 50%;
  transform: translateX(-50%);
  text-align: center;
  padding: 50px 80px;
}
.result-board h1 {
  font-size: 3.5rem;
  margin: 0 0 20px 0;
  font-family: 'Fredoka', sans-serif;
}
.red-text { color: #FF5252 !important; }
.blue-text { color: #448AFF !important; }

/* ================== ROPE ARENA ================== */
.rope-container {
  height: 300px;
  position: relative;
  overflow: hidden;
  flex-shrink: 0;
}

.center-line {
  position: absolute;
  left: 50%;
  top: 0; bottom: 0;
  width: 4px;
  background: rgba(255,255,255,0.35);
  border: 2px dashed rgba(255,255,255,0.6);
  z-index: 5;
  pointer-events: none;
}

.win-line {
  position: absolute;
  top: 0; bottom: 0;
  width: 6px;
  background: linear-gradient(to bottom, #FFEB3B, #FFC107);
  box-shadow: 0 0 16px 4px #FFEB3B;
  z-index: 5;
  display: flex;
  align-items: flex-start;
  justify-content: center;
  pointer-events: none;
}
.win-label {
  font-size: 1.6rem;
  margin-top: 10px;
  filter: drop-shadow(0 2px 4px black);
}
.left-line  { left: 14%; }
.right-line { right: 14%; }

/* ---- BRAIDED ROPE (full width) ---- */
.rope-full {
  position: absolute;
  left: 0; right: 0;
  top: 50%;
  transform: translateY(-50%);
  height: 42px;
  z-index: 2;
}

.rope-strand {
  position: absolute;
  left: 0; right: 0;
  height: 14px;
  border-radius: 7px;
}
.strand-1 {
  top: 0;
  background: repeating-linear-gradient(60deg, #8B5E3C 0px, #C49A6C 8px, #6B3F1E 16px, #A0714A 24px, #8B5E3C 32px);
  box-shadow: 0 4px 8px rgba(0,0,0,0.5);
}
.strand-2 {
  top: 14px;
  background: repeating-linear-gradient(-60deg, #7A4F2B 0px, #B8895A 8px, #5C3318 16px, #96623A 24px, #7A4F2B 32px);
  box-shadow: 0 2px 6px rgba(0,0,0,0.4);
}
.strand-3 {
  top: 28px;
  background: repeating-linear-gradient(60deg, #8B5E3C 0px, #C49A6C 8px, #6B3F1E 16px, #A0714A 24px, #8B5E3C 32px);
  box-shadow: 0 -2px 8px rgba(0,0,0,0.4);
}

/* SLIDING FLAG: Indonesian flag */
.rope-flag {
  position: absolute;
  top: calc(50% - 55px);
  transform: translateX(-50%);
  width: 52px;
  height: 36px;
  z-index: 10;
  border-radius: 4px;
  overflow: hidden;
  box-shadow: 0 0 14px 4px rgba(255,255,255,0.4), 0 4px 10px rgba(0,0,0,0.5);
  transition: left 0.35s cubic-bezier(0.22, 1, 0.36, 1);
  border: 2px solid rgba(255,255,255,0.5);
}
.flag-red {
  height: 50%;
  background: #E53935;
}
.flag-white {
  height: 50%;
  background: #FFFFFF;
}
.flag-pole {
  position: absolute;
  left: 50%;
  top: 100%;
  transform: translateX(-50%);
  width: 5px;
  height: 60px;
  background: linear-gradient(#bbb, #888);
  border-radius: 3px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.4);
}

/* Hand-rope connectors */
.hand-rope {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  height: 8px;
  background: repeating-linear-gradient(90deg, #7A4F2B 0px, #B8895A 6px, #7A4F2B 12px);
  border-radius: 4px;
  z-index: 4;
  box-shadow: 0 2px 4px rgba(0,0,0,0.4);
}
.red-hand-rope  { left: 0; width: 32%; }
.blue-hand-rope { right: 0; width: 32%; }

/* ---- TEAM FIGURES ---- */
.team {
  position: absolute;
  top: 95px;
  display: flex;
  align-items: flex-start;
  z-index: 6;
  gap: 0px;
}
.red-team  { left:  1%; }
.blue-team { right: 1%; }

.person-svg {
  width: 60px;
  height: 110px;
  animation: pullSway 0.7s ease-in-out infinite alternate;
}
.person-svg:nth-child(2) { animation-delay: 0.1s; }
.person-svg:nth-child(3) { animation-delay: 0.2s; }
.person-svg:nth-child(4) { animation-delay: 0.3s; }
.person-svg:nth-child(5) { animation-delay: 0.4s; }

@keyframes pullSway {
  0%   { transform: rotate(-4deg) translateY(0); }
  100% { transform: rotate(4deg) translateY(-5px); }
}
.red-person  { filter: drop-shadow(0 4px 6px rgba(0,0,0,0.5)) drop-shadow(0 0 8px rgba(220,50,50,0.5)); }
.blue-person { filter: drop-shadow(0 4px 6px rgba(0,0,0,0.5)) drop-shadow(0 0 8px rgba(50,100,255,0.5)); }

/* CONTROLS AREA */
.split-controls {
  display: flex;
  height: 35vh;
  min-height: 250px;
  background: rgba(0,0,0,0.7);
  backdrop-filter: blur(10px);
  position: relative;
}

.divider {
  width: 60px;
  background: #FFC107;
  color: black;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 900;
  font-size: 2rem;
  font-family: 'Fredoka', cursive;
  box-shadow: 0 0 20px rgba(0,0,0,0.5);
  z-index: 3;
}

.control-panel {
  flex: 1;
  padding: 30px;
  display: flex;
  flex-direction: column;
}

.red-panel { border-top: 5px solid #F44336; }
.blue-panel { border-top: 5px solid #2196F3; }

.panel-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  color: white;
}
.panel-header h3 {
  margin: 0;
  font-family: 'Fredoka', sans-serif;
  font-size: 2rem;
}
.red-panel h3 { color: #FF8A80; text-shadow: 0 0 10px #D50000; }
.blue-panel h3 { color: #82B1FF; text-shadow: 0 0 10px #2962FF; }

.stun-warn {
  color: #FF5252;
  font-weight: bold;
  animation: blink 0.5s infinite;
  font-size: 1.2rem;
}

@keyframes blink {
  0% { opacity: 1; }
  50% { opacity: 0.2; }
}

.buttons-grid {
  display: flex;
  gap: 20px;
  flex-grow: 1;
}

.btn-team {
  flex: 1;
  font-size: 4rem;
  border-radius: 20px;
  border: none;
  color: white;
  font-family: 'Fredoka', cursive;
  font-weight: bold;
  cursor: pointer;
  transition: all 0.1s;
}

.btn-red {
  background: linear-gradient(#f44336, #b71c1c);
  box-shadow: 0 10px 0 #b71c1c;
}
.btn-red:active:not(:disabled) {
  transform: translateY(10px);
  box-shadow: 0 0 0 #b71c1c;
}

.btn-blue {
  background: linear-gradient(#2196f3, #0d47a1);
  box-shadow: 0 10px 0 #0d47a1;
}
.btn-blue:active:not(:disabled) {
  transform: translateY(10px);
  box-shadow: 0 0 0 #0d47a1;
}

.btn-team:disabled {
  filter: grayscale(1);
  opacity: 0.5;
  cursor: not-allowed;
  transform: translateY(10px);
  box-shadow: 0 0 0 black;
}
</style>
