<script setup>
import { ref, computed, onMounted } from 'vue';

const emit  = defineEmits(['back']);
const props = defineProps({
  level: { type: Object, default: () => ({ id: 1, name: 'Bintang Satu', range: '1–5', min: 1, max: 5 }) }
});

// ─── Game State ───────────────────────────────────────────────
const maxProgress  = 100;
const redProgress  = ref(0);
const blueProgress = ref(0);

const redStunned   = ref(false);
const blueStunned  = ref(false);

const questionText  = ref('');
const choices       = ref([]);
const correctAnswer = ref(0);

const gameState = ref('playing'); // 'playing' | 'red_wins' | 'blue_wins'

// ─── Calculator inputs ────────────────────────────────────────
const redInput  = ref('');
const blueInput = ref('');

// Red numpad
function redPress(key) {
  if (gameState.value !== 'playing' || redStunned.value || countdownActive.value) return;
  if (key === '⌫') { redInput.value = redInput.value.slice(0, -1); return; }
  if (redInput.value.length < 4) redInput.value += key;
}
function redSubmit() {
  if (gameState.value !== 'playing' || redStunned.value || countdownActive.value) return;
  if (!redInput.value) return;
  const val = parseInt(redInput.value);
  redInput.value = '';
  if (val === correctAnswer.value) {
    redProgress.value = Math.min(maxProgress, redProgress.value + 5);
    triggerSmoke(redSmoke);
    generateQuestion();
    checkWin();
  } else {
    redProgress.value = Math.max(0, redProgress.value - 4);
    redStunned.value = true;
    setTimeout(() => { redStunned.value = false; }, 1500);
    checkWin();
  }
}

// Blue numpad
function bluePress(key) {
  if (gameState.value !== 'playing' || blueStunned.value || countdownActive.value) return;
  if (key === '⌫') { blueInput.value = blueInput.value.slice(0, -1); return; }
  if (blueInput.value.length < 4) blueInput.value += key;
}
function blueSubmit() {
  if (gameState.value !== 'playing' || blueStunned.value || countdownActive.value) return;
  if (!blueInput.value) return;
  const val = parseInt(blueInput.value);
  blueInput.value = '';
  if (val === correctAnswer.value) {
    blueProgress.value = Math.min(maxProgress, blueProgress.value + 5);
    triggerSmoke(blueSmoke);
    generateQuestion();
    checkWin();
  } else {
    blueProgress.value = Math.max(0, blueProgress.value - 4);
    blueStunned.value = true;
    setTimeout(() => { blueStunned.value = false; }, 1500);
    checkWin();
  }
}

// ─── Countdown ───────────────────────────────────────────────
const countdownActive = ref(true);
const countdownValue  = ref(3); // 3 → 2 → 1 → 'GO!'

function startCountdown() {
  countdownActive.value = true;
  countdownValue.value  = 3;
  const steps = [3, 2, 1, 'SELAMAT\nBALAPAN! 🏁'];
  let i = 0;
  const tick = () => {
    countdownValue.value = steps[i];
    i++;
    if (i < steps.length) {
      setTimeout(tick, 900);
    } else {
      // Show GO for a bit then hide overlay
      setTimeout(() => { countdownActive.value = false; }, 700);
    }
  };
  tick();
}

// smoke particles for each car
const redSmoke  = ref(false);
const blueSmoke = ref(false);

// ─── Helpers ─────────────────────────────────────────────────
function rnd(min, max) { return Math.floor(Math.random() * (max - min + 1)) + min; }

function rndForLevel() {
  const ranges = { 1: [1,5], 2: [1,7], 3: [1,9], 4: [1,10] };
  const [min, max] = ranges[props.level?.id ?? 1] ?? [1,5];
  return rnd(min, max);
}

function generateQuestion() {
  const a = rndForLevel(), b = rndForLevel();
  correctAnswer.value = a * b;
  questionText.value  = `${a} × ${b}`;
  // Clear both inputs on new question
  redInput.value  = '';
  blueInput.value = '';
}

function triggerSmoke(teamRef) {
  teamRef.value = true;
  setTimeout(() => { teamRef.value = false; }, 600);
}

function answerRed(choice) {
  if (gameState.value !== 'playing' || redStunned.value || countdownActive.value) return;
  if (choice === correctAnswer.value) {
    redProgress.value = Math.min(maxProgress, redProgress.value + 1);
    triggerSmoke(redSmoke);
    generateQuestion();
    checkWin();
  } else {
    redProgress.value = Math.max(0, redProgress.value - 4);
    redStunned.value = true;
    setTimeout(() => { redStunned.value = false; }, 1500);
    checkWin();
  }
}

function answerBlue(choice) {
  if (gameState.value !== 'playing' || blueStunned.value || countdownActive.value) return;
  if (choice === correctAnswer.value) {
    blueProgress.value = Math.min(maxProgress, blueProgress.value + 1);
    triggerSmoke(blueSmoke);
    generateQuestion();
    checkWin();
  } else {
    blueProgress.value = Math.max(0, blueProgress.value - 4);
    blueStunned.value = true;
    setTimeout(() => { blueStunned.value = false; }, 1500);
    checkWin();
  }
}

function checkWin() {
  if (redProgress.value  >= maxProgress) gameState.value = 'red_wins';
  if (blueProgress.value >= maxProgress) gameState.value = 'blue_wins';
}

function restart() {
  redProgress.value  = 0;
  blueProgress.value = 0;
  redStunned.value   = false;
  blueStunned.value  = false;
  gameState.value    = 'playing';
  generateQuestion();
  startCountdown();
}

onMounted(() => {
  generateQuestion();
  startCountdown();
});

// ─── Computed styles ─────────────────────────────────────────
// Car moves from 2% to 82% (leaving room for car width & finish zone)
const redCarStyle  = computed(() => ({ left: `${2 + redProgress.value  * 0.80}%` }));
const blueCarStyle = computed(() => ({ left: `${2 + blueProgress.value * 0.80}%` }));
</script>

<template>
  <div class="race-arena">
    <div class="vignette"></div>

    <!-- Countdown overlay -->
    <transition name="cd-overlay">
      <div v-if="countdownActive" class="countdown-overlay">
        <transition name="cd-num" mode="out-in">
          <div :key="countdownValue" class="countdown-number"
            :class="{
              'cd-red':  countdownValue === 3,
              'cd-yellow': countdownValue === 2,
              'cd-green': countdownValue === 1,
              'cd-go':   typeof countdownValue === 'string'
            }"
          >
            {{ countdownValue }}
          </div>
        </transition>
        <div class="countdown-subtext">
          {{ typeof countdownValue === 'string' ? '' : 'Bersiap...' }}
        </div>
      </div>
    </transition>

    <!-- Back button -->
    <button class="btn-back" @click="emit('back')">Kembali</button>

    <!-- Level badge -->
    <div class="level-badge">{{ props.level?.emoji ?? '⭐' }} {{ props.level?.name ?? 'Bintang Satu' }}</div>

    <!-- ─── QUESTION BOARD ─── inside track, at vertical center ─── -->
    <!-- Shown as overlay on the track-area, vertically between the two lanes -->

    <!-- ─── RESULT (shown when game over) ─── -->
    <div v-if="gameState !== 'playing'" class="result-board glass-panel-dark">
      <div class="trophy">🏆</div>
      <h1 :class="gameState === 'red_wins' ? 'red-text' : 'blue-text'">
        TIM {{ gameState === 'red_wins' ? 'MERAH' : 'BIRU' }} MENANG!
      </h1>
      <p>Selamat! Mobil juara sudah menyentuh garis finish!</p>
      <button class="btn-choice correct" @click="restart">Main Lagi 🔁</button>
    </div>

    <!-- ─── RACE TRACK ─── -->
    <div class="track-area">

      <!-- Finish line -->
      <div class="finish-line">
        <div class="checker-col" v-for="n in 10" :key="n"></div>
        <span class="finish-flag">🏁</span>
      </div>

      <!-- Start line -->
      <div class="start-line"></div>

      <!-- ─── QUESTION BOARD ─── centered between the two lanes ─── -->
      <div v-if="gameState === 'playing'" class="question-board-track glass-panel-dark">
        <span class="q-label">Siapa Cepat Dia Gas! 🏎️</span>
        <h1 class="q-text bounce">{{ questionText }} = ?</h1>
      </div>

      <!-- ─── RED CAR LANE ─── -->
      <div class="lane lane-red">
        <!-- Team name badge centered in lane -->
        <div class="lane-team-name red-team-name">🔴 TIM MERAH</div>

        <!-- Progress track -->
        <div class="track-ground">
          <!-- road dashes -->
          <div class="road-dash" v-for="n in 8" :key="n"></div>
        </div>

        <!-- Car -->
        <div class="car-wrapper" :style="redCarStyle" :class="{ stunned: redStunned }">
          <!-- SVG Red Car (top-down) -->
          <svg class="car-svg" viewBox="0 0 100 56" xmlns="http://www.w3.org/2000/svg">
            <!-- Shadow -->
            <ellipse cx="50" cy="52" rx="38" ry="5" fill="rgba(0,0,0,0.3)"/>
            <!-- Body -->
            <rect x="8"  y="16" width="84" height="26" rx="8"  fill="#E53935"/>
            <rect x="18" y="8"  width="60" height="22" rx="8"  fill="#EF5350"/>
            <!-- Windshield -->
            <rect x="26" y="10" width="22" height="14" rx="4"  fill="#B3E5FC" opacity="0.9"/>
            <!-- Rear window -->
            <rect x="56" y="10" width="18" height="14" rx="4"  fill="#B3E5FC" opacity="0.7"/>
            <!-- Spoiler -->
            <rect x="82" y="14" width="10" height="16" rx="3"  fill="#B71C1C"/>
            <rect x="78" y="12" width="14" height="4"  rx="2"  fill="#D32F2F"/>
            <!-- Wheels -->
            <circle cx="24" cy="42" r="9"  fill="#212121"/><circle cx="24" cy="42" r="4"  fill="#757575"/>
            <circle cx="76" cy="42" r="9"  fill="#212121"/><circle cx="76" cy="42" r="4"  fill="#757575"/>
            <circle cx="24" cy="14" r="8"  fill="#212121"/><circle cx="24" cy="14" r="3.5" fill="#757575"/>
            <circle cx="76" cy="14" r="8"  fill="#212121"/><circle cx="76" cy="14" r="3.5" fill="#757575"/>
            <!-- Headlights -->
            <ellipse cx="12" cy="24" rx="5" ry="4" fill="#FFF9C4"/>
            <ellipse cx="12" cy="32" rx="5" ry="4" fill="#FFF9C4"/>
            <!-- Number plate -->
            <rect x="36" y="22" width="24" height="12" rx="3" fill="white"/>
            <text x="48" y="32" text-anchor="middle" font-size="9" font-weight="bold" fill="#E53935">MERAH</text>
          </svg>
          <!-- Smoke puff on correct answer -->
          <transition name="smoke">
            <div v-if="redSmoke" class="smoke-puff red-smoke"></div>
          </transition>
        </div>
      </div>

      <!-- ─── BLUE CAR LANE ─── -->
      <div class="lane lane-blue">
        <!-- Team name badge centered in lane -->
        <div class="lane-team-name blue-team-name">🔵 TIM BIRU</div>

        <div class="track-ground">
          <div class="road-dash" v-for="n in 8" :key="n"></div>
        </div>

        <div class="car-wrapper" :style="blueCarStyle" :class="{ stunned: blueStunned }">
          <svg class="car-svg" viewBox="0 0 100 56" xmlns="http://www.w3.org/2000/svg">
            <ellipse cx="50" cy="52" rx="38" ry="5" fill="rgba(0,0,0,0.3)"/>
            <rect x="8"  y="16" width="84" height="26" rx="8"  fill="#1E88E5"/>
            <rect x="18" y="8"  width="60" height="22" rx="8"  fill="#42A5F5"/>
            <rect x="26" y="10" width="22" height="14" rx="4"  fill="#B3E5FC" opacity="0.9"/>
            <rect x="56" y="10" width="18" height="14" rx="4"  fill="#B3E5FC" opacity="0.7"/>
            <rect x="82" y="14" width="10" height="16" rx="3"  fill="#0D47A1"/>
            <rect x="78" y="12" width="14" height="4"  rx="2"  fill="#1565C0"/>
            <circle cx="24" cy="42" r="9"  fill="#212121"/><circle cx="24" cy="42" r="4"  fill="#757575"/>
            <circle cx="76" cy="42" r="9"  fill="#212121"/><circle cx="76" cy="42" r="4"  fill="#757575"/>
            <circle cx="24" cy="14" r="8"  fill="#212121"/><circle cx="24" cy="14" r="3.5" fill="#757575"/>
            <circle cx="76" cy="14" r="8"  fill="#212121"/><circle cx="76" cy="14" r="3.5" fill="#757575"/>
            <ellipse cx="12" cy="24" rx="5" ry="4" fill="#FFF9C4"/>
            <ellipse cx="12" cy="32" rx="5" ry="4" fill="#FFF9C4"/>
            <rect x="36" y="22" width="24" height="12" rx="3" fill="white"/>
            <text x="48" y="32" text-anchor="middle" font-size="9" font-weight="bold" fill="#1E88E5">BIRU</text>
          </svg>
          <transition name="smoke">
            <div v-if="blueSmoke" class="smoke-puff blue-smoke"></div>
          </transition>
        </div>
      </div>

    </div><!-- end track-area -->

    <!-- ─── CONTROLS ─── -->
    <div class="split-controls">

      <!-- RED calculator -->
      <div class="control-panel red-panel">
        <div class="panel-header">
          <h3>🔴 TIM MERAH</h3>
          <span v-if="redStunned" class="stun-warn">⚠️ Salah! Tunggu...</span>
        </div>
        <!-- Display -->
        <div class="calc-display" :class="{ stunned: redStunned }">
          <span class="calc-expr">{{ questionText }} =</span>
          <span class="calc-input">{{ redInput || '_' }}</span>
        </div>
        <!-- Numpad -->
        <div class="numpad">
          <button v-for="k in ['7','8','9','4','5','6','1','2','3','⌫','0','✓']" :key="'r'+k"
            :class="['numpad-btn', k==='✓' ? 'btn-submit btn-submit-red' : k==='⌫' ? 'btn-del' : 'btn-digit']"
            :disabled="redStunned || gameState !== 'playing' || countdownActive"
            @click="k === '✓' ? redSubmit() : redPress(k)"
          >{{ k }}</button>
        </div>
      </div>

      <div class="divider">VS</div>

      <!-- BLUE calculator -->
      <div class="control-panel blue-panel">
        <div class="panel-header">
          <h3>🔵 TIM BIRU</h3>
          <span v-if="blueStunned" class="stun-warn">⚠️ Salah! Tunggu...</span>
        </div>
        <!-- Display -->
        <div class="calc-display" :class="{ stunned: blueStunned }">
          <span class="calc-expr">{{ questionText }} =</span>
          <span class="calc-input">{{ blueInput || '_' }}</span>
        </div>
        <!-- Numpad -->
        <div class="numpad">
          <button v-for="k in ['7','8','9','4','5','6','1','2','3','⌫','0','✓']" :key="'b'+k"
            :class="['numpad-btn', k==='✓' ? 'btn-submit btn-submit-blue' : k==='⌫' ? 'btn-del' : 'btn-digit']"
            :disabled="blueStunned || gameState !== 'playing' || countdownActive"
            @click="k === '✓' ? blueSubmit() : bluePress(k)"
          >{{ k }}</button>
        </div>
      </div>

    </div>
  </div>
</template>

<style scoped>
/* ── Layout ── */
.race-arena {
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
  background: linear-gradient(180deg, #1a1a2e 0%, #16213e 100%);
  position: relative;
  overflow: hidden;
  font-family: 'Fredoka', sans-serif;
}

.vignette {
  position: absolute; inset: 0;
  background: radial-gradient(circle, transparent 40%, rgba(0,0,0,0.55) 100%);
  pointer-events: none; z-index: 1;
}

.btn-back {
  position: absolute; top: 18px; left: 20px; z-index: 100;
  background: rgba(255, 255, 255, 0.12);
  backdrop-filter: blur(12px);
  border: 1px solid rgba(255, 255, 255, 0.25);
  padding: 10px 24px;
  border-radius: 99px;
  color: white;
  font-family: 'Fredoka', sans-serif;
  font-size: 1.1rem; font-weight: 700;
  cursor: pointer;
  display: flex; align-items: center; gap: 10px;
  transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  box-shadow: 0 4px 15px rgba(0,0,0,0.3);
}

.btn-back::before {
  content: '←';
  display: inline-block;
  font-size: 1.4rem;
  transition: transform 0.3s;
}

.btn-back:hover {
  background: rgba(255, 255, 255, 0.22);
  transform: translateY(-3px);
  box-shadow: 0 8px 25px rgba(0,0,0,0.4), 0 0 15px rgba(255,255,255,0.1);
  border-color: rgba(255, 255, 255, 0.5);
}

.btn-back:hover::before {
  transform: translateX(-5px);
}

.level-badge {
  position: absolute; top: 14px; right: 16px; z-index: 100;
  background: rgba(255,255,255,0.15);
  border: 2px solid rgba(255,255,255,0.35);
  color: white; padding: 8px 18px;
  border-radius: 20px;
  font-family: 'Fredoka', sans-serif;
  font-size: 1.1rem; font-weight: bold;
  backdrop-filter: blur(10px);
}

/* ── Question inside track (centered between 2 lanes) ── */
.question-board-track {
  position: absolute;
  top: 50%; left: 50%;
  transform: translate(-50%, -50%);
  text-align: center; padding: 8px 36px;
  z-index: 20; white-space: nowrap;
  pointer-events: none;
}
.q-label { font-size: 0.9rem; color: #FFF176; display: block; margin-bottom: 2px; }
.q-text  { font-size: 2.8rem; margin: 0; color: white; }

/* ── Team name centered inside each lane ── */
.lane-team-name {
  position: absolute;
  left: 50%; top: 50%;
  transform: translate(-50%, -50%);
  font-family: 'Fredoka', sans-serif;
  font-size: 1.1rem; font-weight: bold;
  letter-spacing: 1px;
  background: rgba(0,0,0,0.35);
  padding: 4px 16px; border-radius: 20px;
  pointer-events: none; z-index: 4;
  white-space: nowrap;
  backdrop-filter: blur(4px);
}
.red-team-name  { color: #FF8A80; border: 1px solid rgba(244,67,54,0.4); }
.blue-team-name { color: #82B1FF; border: 1px solid rgba(33,150,243,0.4); }

.result-board {
  position: absolute; top: 20px; left: 50%; transform: translateX(-50%);
  text-align: center; padding: 30px 60px; z-index: 20;
}
.trophy    { font-size: 5rem; }
.result-board h1 { font-size: 2.8rem; margin: 0 0 8px; }
.result-board p  { font-size: 1.2rem; margin-bottom: 20px; color: #eee; }
.red-text  { color: #FF5252; }
.blue-text { color: #448AFF; }

.bounce { animation: bounceScale 0.8s ease-in-out infinite alternate; }
@keyframes bounceScale { from { transform: scale(1); } to { transform: scale(1.08); } }

/* ── Track area ── */
.track-area {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  position: relative;
  z-index: 5;
  gap: 6px;
  padding: 6px 0;
  max-height: 52vh;
}

/* Finish & Start lines */
.finish-line {
  position: absolute;
  right: 14%;
  top: 0; bottom: 0;
  width: 28px;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  z-index: 8;
}
.checker-col { flex: 1; background: repeating-linear-gradient(180deg, black 0%, black 50%, white 50%, white 100%); }
.checker-col:nth-child(even) { background: repeating-linear-gradient(180deg, white 0%, white 50%, black 50%, black 100%); }
.finish-flag {
  position: absolute; top: -24px; left: 50%; transform: translateX(-50%);
  font-size: 2rem; filter: drop-shadow(0 2px 4px black);
}

.start-line {
  position: absolute;
  left: 3%;
  top: 0; bottom: 0;
  width: 6px;
  background: white;
  z-index: 8;
  border-radius: 3px;
  opacity: 0.7;
}

/* ── Individual lane ── */
.lane {
  flex: 1;
  position: relative;
  border-radius: 12px;
  margin: 0 1%;
  overflow: hidden;
  min-height: 80px;
}

.lane-red  { background: linear-gradient(90deg, #b71c1c22, #f4433622, #b71c1c22); border: 2px solid rgba(244,67,54,0.5); }
.lane-blue { background: linear-gradient(90deg, #0d47a122, #2196f322, #0d47a122); border: 2px solid rgba(33,150,243,0.5); }

/* Lane label */
.lane-edge {
  position: absolute; left: 0; top: 50%; transform: translateY(-50%);
  writing-mode: vertical-rl; text-orientation: mixed;
  font-size: 0.9rem; font-weight: bold; padding: 4px 6px;
  letter-spacing: 1px; opacity: 0.7;
}
.red-edge  { color: #FF8A80; }
.blue-edge { color: #82B1FF; }

/* Road dashes */
.track-ground {
  display: flex;
  align-items: center;
  position: absolute; inset: 0;
  gap: 0;
  padding: 0 3%;
}
.road-dash {
  flex: 1;
  height: 3px;
  background: rgba(255,255,255,0.15);
  border-radius: 2px;
  margin: 0 6px;
}

/* ── Car wrapper ── */
.car-wrapper {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  transition: left 0.35s cubic-bezier(0.22, 1, 0.36, 1);
  width: 100px;
}

.car-wrapper.stunned {
  animation: swerve 0.4s ease-in-out;
}

@keyframes swerve {
  0%   { transform: translateY(-50%) rotate(0deg); }
  25%  { transform: translateY(calc(-50% - 10px)) rotate(-8deg); }
  75%  { transform: translateY(calc(-50% + 10px)) rotate(8deg); }
  100% { transform: translateY(-50%) rotate(0deg); }
}

.car-svg {
  width: 100%;
  height: auto;
  filter: drop-shadow(0 6px 8px rgba(0,0,0,0.6));
  animation: carBounce 0.3s ease-in-out infinite alternate;
}
@keyframes carBounce {
  from { transform: translateY(0); }
  to   { transform: translateY(-3px); }
}

/* Smoke on accelerate */
.smoke-puff {
  position: absolute;
  right: 90%;
  top: 50%;
  transform: translateY(-50%);
  width: 30px; height: 30px;
  border-radius: 50%;
  pointer-events: none;
}
.red-smoke  { background: radial-gradient(circle, rgba(255,100,100,0.8), transparent); }
.blue-smoke { background: radial-gradient(circle, rgba(100,150,255,0.8), transparent); }

.smoke-enter-active { transition: all 0.5s ease; }
.smoke-leave-active { transition: all 0.4s ease; }
.smoke-enter-from   { opacity: 0; transform: translateY(-50%) scale(0.2); }
.smoke-leave-to     { opacity: 0; transform: translateY(-80%) scale(1.8); }

/* ── Controls ── */
.split-controls {
  display: flex;
  height: 48vh;
  min-height: 260px;
  background: rgba(0,0,0,0.75);
  backdrop-filter: blur(10px);
  border-top: 2px solid rgba(255,255,255,0.1);
  position: relative;
  z-index: 10;
  flex-shrink: 0;
}

.divider {
  width: 60px;
  background: #FFC107;
  color: #1a1a1a;
  display: flex; align-items: center; justify-content: center;
  font-weight: 900; font-size: 2rem;
  box-shadow: 0 0 20px rgba(255,193,7,0.4);
}

.control-panel {
  flex: 1; padding: 14px 18px;
  display: flex; flex-direction: column; gap: 10px;
}

.red-panel  { border-top: 5px solid #F44336; }
.blue-panel { border-top: 5px solid #2196F3; }

.panel-header {
  display: flex; justify-content: space-between; align-items: center;
}
.panel-header h3 {
  margin: 0; font-size: 1.5rem;
  font-family: 'Fredoka', sans-serif; color: white;
}
.stun-warn {
  color: #FF5252; font-weight: bold; font-size: 0.9rem;
  animation: blink 0.4s infinite;
}
@keyframes blink { 0%,100% { opacity:1; } 50% { opacity:0.15; } }

/* Calculator display */
.calc-display {
  background: rgba(0,0,0,0.5);
  border: 2px solid rgba(255,255,255,0.15);
  border-radius: 10px;
  padding: 8px 14px;
  display: flex; align-items: center; justify-content: space-between;
  min-height: 44px;
  transition: border-color 0.3s;
}
.calc-display.stunned {
  border-color: rgba(255,82,82,0.7);
  background: rgba(255,82,82,0.1);
}
.calc-expr {
  font-size: 1.1rem; color: rgba(255,255,255,0.55);
  font-family: 'Fredoka', sans-serif;
}
.calc-input {
  font-size: 1.8rem; font-weight: bold; color: white;
  font-family: 'Fredoka', sans-serif;
  letter-spacing: 2px;
}

/* Numpad 3-col grid */
.numpad {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 5px;
  flex: 1;
  min-height: 0;
}

.numpad-btn {
  border: none; border-radius: 10px; cursor: pointer;
  font-family: 'Fredoka', sans-serif; font-size: 1.3rem; font-weight: bold;
  color: white; transition: all 0.1s; min-height: 0;
}
.numpad-btn:active:not(:disabled) { transform: scale(0.92); }
.numpad-btn:disabled { opacity: 0.35; cursor: not-allowed; }

.btn-digit {
  background: rgba(255,255,255,0.12);
  border: 1px solid rgba(255,255,255,0.2);
  box-shadow: 0 3px 0 rgba(0,0,0,0.3);
}
.btn-digit:hover:not(:disabled) { background: rgba(255,255,255,0.22); }

.btn-del {
  background: rgba(255,152,0,0.3);
  border: 1px solid rgba(255,152,0,0.5);
  box-shadow: 0 3px 0 rgba(0,0,0,0.3);
  font-size: 1.1rem;
}
.btn-del:hover:not(:disabled) { background: rgba(255,152,0,0.5); }

.btn-submit { font-size: 1.6rem; }
.btn-submit-red  {
  background: linear-gradient(180deg, #F44336, #b71c1c);
  box-shadow: 0 4px 0 #7f0000;
}
.btn-submit-blue {
  background: linear-gradient(180deg, #2196F3, #0d47a1);
  box-shadow: 0 4px 0 #002171;
}
.btn-submit:active:not(:disabled) { transform: scale(0.92); box-shadow: none !important; }

/* Re-use global glass-panel-dark if not scoped */
.glass-panel-dark {
  background: rgba(0,0,0,0.65);
  backdrop-filter: blur(14px);
  border: 1px solid rgba(255,255,255,0.12);
  border-radius: 20px;
  box-shadow: 0 8px 32px rgba(0,0,0,0.5);
}

/* ── Countdown Overlay ── */
.countdown-overlay {
  position: fixed; inset: 0; z-index: 200;
  background: rgba(0, 0, 0, 0.78);
  display: flex; flex-direction: column;
  align-items: center; justify-content: center;
  backdrop-filter: blur(6px);
}

.countdown-number {
  font-family: 'Fredoka', sans-serif;
  font-size: 22vw;
  font-weight: 900;
  line-height: 1;
  text-align: center;
  white-space: pre-line;
  text-shadow: 0 0 60px currentColor, 0 8px 24px rgba(0,0,0,0.8);
  filter: drop-shadow(0 0 40px currentColor);
}

.cd-red    { color: #FF5252; }
.cd-yellow { color: #FFC107; }
.cd-green  { color: #69F0AE; }
.cd-go     { font-size: 12vw; color: #FFFFFF; }

.countdown-subtext {
  font-family: 'Fredoka', sans-serif;
  font-size: 1.8rem;
  color: rgba(255,255,255,0.5);
  margin-top: 10px;
  letter-spacing: 4px;
  text-transform: uppercase;
}

/* Transition: number pops in and zooms out */
.cd-num-enter-active {
  animation: cdPop 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}
.cd-num-leave-active {
  animation: cdZoomOut 0.3s ease-in forwards;
}
@keyframes cdPop {
  from { transform: scale(0.3); opacity: 0; }
  to   { transform: scale(1);   opacity: 1; }
}
@keyframes cdZoomOut {
  from { transform: scale(1);   opacity: 1; }
  to   { transform: scale(2.5); opacity: 0; }
}

/* Overlay fade in/out */
.cd-overlay-enter-active { transition: opacity 0.3s ease; }
.cd-overlay-leave-active { transition: opacity 0.5s ease; }
.cd-overlay-enter-from, .cd-overlay-leave-to { opacity: 0; }
</style>
