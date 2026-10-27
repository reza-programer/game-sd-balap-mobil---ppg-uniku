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

const COUNTDOWN_STEP_MS = 1000; // each of 3 · 2 · 1
const GO_MESSAGE_MS     = 1800; // "SELAMAT BALAPAN!" stays long enough to read

function startCountdown() {
  countdownActive.value = true;
  countdownValue.value  = 3;
  const steps = [3, 2, 1, 'SELAMAT\nBALAPAN! 🏁'];
  let i = 0;
  const tick = () => {
    countdownValue.value = steps[i];
    i++;
    if (i < steps.length) {
      setTimeout(tick, COUNTDOWN_STEP_MS);
    } else {
      // Show GO for a bit then hide overlay
      setTimeout(() => { countdownActive.value = false; }, GO_MESSAGE_MS);
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
// Car rides between the start line and the finish line; at 100% its nose touches the finish
const carLeft = progress => ({ left: `calc((100% - var(--car-w)) * ${progress / maxProgress})` });
const redCarStyle  = computed(() => carLeft(redProgress.value));
const blueCarStyle = computed(() => carLeft(blueProgress.value));

const levelColor = computed(() => props.level?.color ?? '#4CAF50');
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

    <!-- ─── TOP BAR ─── back · question · level ─── -->
    <header class="top-bar">
      <button class="btn-back" @click="emit('back')">Kembali</button>

      <!-- Hidden (not removed) after the race so the top bar keeps its height -->
      <div class="question-card" :class="{ 'is-hidden': gameState !== 'playing' }">
        <span class="q-label">Siapa Cepat Dia Gas!</span>
        <span class="q-text bounce">{{ questionText }} = ?</span>
      </div>

      <div class="level-badge" :style="{ '--accent': levelColor }">
        <span class="level-dot"></span>
        Level {{ props.level?.id ?? 1 }} · {{ props.level?.name ?? 'Mudah' }}
      </div>
    </header>

    <!-- ─── RESULT (shown when game over) ─── -->
    <div v-if="gameState !== 'playing'" class="result-overlay">
      <div class="result-card" :class="gameState === 'red_wins' ? 'red-win' : 'blue-win'">
        <div class="trophy">🏆</div>
        <div class="result-eyebrow">Balapan selesai</div>
        <h1>Tim {{ gameState === 'red_wins' ? 'Merah' : 'Biru' }} Menang!</h1>
        <p>Selamat! Mobil juara sudah menyentuh garis finish!</p>
        <div class="result-actions">
          <button class="btn-again" @click="restart">Main Lagi <span class="arrow">↻</span></button>
          <button class="btn-level" @click="emit('back')">Ganti Level</button>
        </div>
      </div>
    </div>

    <!-- ─── RACE TRACK ─── -->
    <div class="track-area">

      <!-- ─── RED CAR LANE ─── -->
      <div class="lane lane-red">
        <div class="lane-tag"><span class="team-dot"></span>Merah</div>

        <div class="road">
          <div class="road-dashes">
            <div class="road-dash" v-for="n in 8" :key="n"></div>
          </div>
          <div class="start-line"></div>
          <div class="finish-line"></div>

          <div class="car-track">
            <div class="car-wrapper" :style="redCarStyle" :class="{ stunned: redStunned }">
              <!-- SVG Red Car (side view, facing the finish line) -->
              <svg class="car-svg" viewBox="0 0 100 56" xmlns="http://www.w3.org/2000/svg">
                <!-- Shadow -->
                <ellipse cx="50" cy="52" rx="38" ry="5" fill="rgba(0,0,0,0.3)"/>
                <!-- Body is drawn nose-left, mirrored so the nose points right -->
                <g transform="translate(100 0) scale(-1 1)">
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
                </g>
                <!-- Number plate (outside the mirror so the text reads normally) -->
                <rect x="37" y="24" width="26" height="11" rx="3" fill="white"/>
                <text x="50" y="32.5" text-anchor="middle" font-size="8" font-weight="bold" fill="#E53935"
                  textLength="21" lengthAdjust="spacingAndGlyphs">MERAH</text>
              </svg>
              <!-- Smoke puff on correct answer -->
              <transition name="smoke">
                <div v-if="redSmoke" class="smoke-puff red-smoke"></div>
              </transition>
            </div>
          </div>
        </div>
      </div>

      <!-- ─── BLUE CAR LANE ─── -->
      <div class="lane lane-blue">
        <div class="lane-tag"><span class="team-dot"></span>Biru</div>

        <div class="road">
          <div class="road-dashes">
            <div class="road-dash" v-for="n in 8" :key="n"></div>
          </div>
          <div class="start-line"></div>
          <div class="finish-line"></div>

          <div class="car-track">
            <div class="car-wrapper" :style="blueCarStyle" :class="{ stunned: blueStunned }">
              <svg class="car-svg" viewBox="0 0 100 56" xmlns="http://www.w3.org/2000/svg">
                <ellipse cx="50" cy="52" rx="38" ry="5" fill="rgba(0,0,0,0.3)"/>
                <g transform="translate(100 0) scale(-1 1)">
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
                </g>
                <rect x="37" y="24" width="26" height="11" rx="3" fill="white"/>
                <text x="50" y="32.5" text-anchor="middle" font-size="8" font-weight="bold" fill="#1E88E5"
                  textLength="17" lengthAdjust="spacingAndGlyphs">BIRU</text>
              </svg>
              <transition name="smoke">
                <div v-if="blueSmoke" class="smoke-puff blue-smoke"></div>
              </transition>
            </div>
          </div>
        </div>
      </div>

    </div><!-- end track-area -->

    <!-- ─── CONTROLS ─── -->
    <div class="split-controls">

      <!-- RED calculator -->
      <div class="control-panel red-panel">
        <!-- Team name + display on one row -->
        <div class="panel-top">
          <div class="team-name"><span class="team-dot"></span>Tim Merah</div>
          <div class="calc-display" :class="{ stunned: redStunned }">
            <span class="calc-expr" :class="{ 'stun-warn': redStunned }">
              {{ redStunned ? 'Salah! Tunggu…' : questionText + ' =' }}
            </span>
            <span class="calc-input">{{ redInput || '_' }}</span>
          </div>
        </div>
        <!-- Numpad -->
        <div class="numpad">
          <button v-for="k in ['7','8','9','4','5','6','1','2','3','⌫','0','✓']" :key="'r'+k"
            :class="['numpad-btn', k==='✓' ? 'btn-submit' : k==='⌫' ? 'btn-del' : 'btn-digit']"
            :disabled="redStunned || gameState !== 'playing' || countdownActive"
            @click="k === '✓' ? redSubmit() : redPress(k)"
          >{{ k }}</button>
        </div>
      </div>

      <div class="divider"><span>VS</span></div>

      <!-- BLUE calculator -->
      <div class="control-panel blue-panel">
        <div class="panel-top">
          <div class="team-name"><span class="team-dot"></span>Tim Biru</div>
          <div class="calc-display" :class="{ stunned: blueStunned }">
            <span class="calc-expr" :class="{ 'stun-warn': blueStunned }">
              {{ blueStunned ? 'Salah! Tunggu…' : questionText + ' =' }}
            </span>
            <span class="calc-input">{{ blueInput || '_' }}</span>
          </div>
        </div>
        <!-- Numpad -->
        <div class="numpad">
          <button v-for="k in ['7','8','9','4','5','6','1','2','3','⌫','0','✓']" :key="'b'+k"
            :class="['numpad-btn', k==='✓' ? 'btn-submit' : k==='⌫' ? 'btn-del' : 'btn-digit']"
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
/* Atas → bawah: top bar (soal), lintasan, kalkulator. Semua bagian dibagi
   dari tinggi layar, jadi selalu muat tanpa terpotong. */
.race-arena {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  background: linear-gradient(135deg, #1a1a2e 0%, #16213e 55%, #0f3460 100%);
  position: relative;
  overflow: hidden;
  font-family: 'Fredoka', sans-serif;
}

.vignette {
  position: absolute; inset: 0;
  background: radial-gradient(ellipse at center, transparent 50%, rgba(0,0,0,0.35) 100%);
  pointer-events: none; z-index: 1;
}

/* Team colours, shared by lanes and keypad panels */
.lane-red,  .red-panel  { --team: #F44336; --team-light: #FF8A80; --team-soft: rgba(244,67,54,0.14); --team-line: rgba(244,67,54,0.45); }
.lane-blue, .blue-panel { --team: #2196F3; --team-light: #82B1FF; --team-soft: rgba(33,150,243,0.14); --team-line: rgba(33,150,243,0.45); }

.team-dot {
  display: inline-block;
  width: 0.75rem; height: 0.75rem;
  border-radius: 50%;
  background: var(--team);
  box-shadow: 0 0 0 0.1875rem var(--team-soft);
  flex-shrink: 0;
}

/* ── Top bar: Kembali · soal · level ── */
.top-bar {
  display: grid;
  grid-template-columns: 1fr auto 1fr;
  align-items: center;
  gap: 1rem;
  padding: 0.75rem 1rem 0.5rem;
  position: relative;
  z-index: 6;
  flex-shrink: 0;
}

.btn-back {
  justify-self: start;
  background: rgba(255, 255, 255, 0.12);
  backdrop-filter: blur(0.75rem);
  border: 0.0625rem solid rgba(255, 255, 255, 0.25);
  padding: 0.625rem 1.5rem;
  border-radius: 99px;
  color: white;
  font-family: 'Fredoka', sans-serif;
  font-size: 1.1rem; font-weight: 700;
  cursor: pointer;
  display: flex; align-items: center; gap: 0.625rem;
  transition: background 0.2s, transform 0.2s;
}
.btn-back::before {
  content: '←';
  display: inline-block;
  font-size: 1.4rem;
  transition: transform 0.3s;
}
.btn-back:hover { background: rgba(255, 255, 255, 0.22); transform: translateY(-0.125rem); }
.btn-back:hover::before { transform: translateX(-0.3125rem); }

.question-card {
  grid-column: 2;
  display: flex; flex-direction: column; align-items: center;
  padding: 0.375rem 2.5rem 0.5rem;
  border-radius: 1rem;
  background: rgba(255,255,255,0.06);
  border: 0.0625rem solid rgba(255,255,255,0.12);
  box-shadow: 0 0.75rem 2rem rgba(0,0,0,0.3);
  white-space: nowrap;
  pointer-events: none;
}
.question-card.is-hidden { visibility: hidden; }
.q-label {
  font-family: 'Plus Jakarta Sans', 'Fredoka', sans-serif;
  font-size: 0.72rem; font-weight: 700;
  color: #FFC107;
  letter-spacing: 0.16em;
  text-transform: uppercase;
}
.q-text {
  font-size: 3rem; font-weight: 700; line-height: 1.1;
  color: white;
}

.level-badge {
  grid-column: 3;
  justify-self: end;
  display: flex; align-items: center; gap: 0.5rem;
  padding: 0.625rem 1.25rem;
  border-radius: 99px;
  background: rgba(255,255,255,0.08);
  border: 0.0625rem solid rgba(255,255,255,0.18);
  color: white;
  font-size: 1.05rem; font-weight: 700;
}
.level-dot {
  width: 0.625rem; height: 0.625rem;
  border-radius: 50%;
  background: var(--accent);
}

.bounce { display: inline-block; animation: bounceScale 0.8s ease-in-out infinite alternate; }
@keyframes bounceScale { from { transform: scale(1); } to { transform: scale(1.05); } }

/* ── Result (game over) ── */
.result-overlay {
  position: absolute; inset: 0; z-index: 50;
  display: flex; align-items: center; justify-content: center;
  background: rgba(8,12,24,0.6);
  backdrop-filter: blur(0.25rem);
}
.result-card {
  min-width: 26rem;
  padding: 2rem 3rem 2.25rem;
  text-align: center;
  color: white;
  border-radius: 1.5rem;
  background: #18223d;
  border: 0.0625rem solid rgba(255,255,255,0.12);
  border-top: 0.375rem solid var(--win);
  box-shadow: 0 2rem 4rem rgba(0,0,0,0.5);
}
.result-card.red-win  { --win: #FF5252; }
.result-card.blue-win { --win: #448AFF; }
.trophy { font-size: 4rem; line-height: 1; }
.result-eyebrow {
  margin-top: 0.75rem;
  font-family: 'Plus Jakarta Sans', 'Fredoka', sans-serif;
  font-size: 0.75rem; font-weight: 700;
  color: #FFC107;
  letter-spacing: 0.16em;
  text-transform: uppercase;
}
.result-card h1 {
  margin: 0.25rem 0 0.375rem;
  font-size: 2.6rem; line-height: 1.1;
  color: var(--win);
}
.result-card p {
  margin: 0 0 1.5rem;
  font-family: 'Plus Jakarta Sans', 'Fredoka', sans-serif;
  font-size: 1rem;
  color: rgba(255,255,255,0.75);
}
.result-actions { display: flex; justify-content: center; gap: 0.75rem; }
.btn-again, .btn-level {
  display: flex; align-items: center; gap: 0.5rem;
  padding: 0.75rem 1.75rem;
  border-radius: 99px;
  font-family: 'Fredoka', sans-serif; font-size: 1.15rem; font-weight: 700;
  cursor: pointer;
  transition: transform 0.15s, background 0.15s;
}
.btn-again {
  border: none;
  background: linear-gradient(135deg, #FFC107, #FF6D00);
  color: #1f1300;
  box-shadow: 0 0.5rem 1.25rem rgba(255,109,0,0.25);
}
.btn-level {
  border: 0.0625rem solid rgba(255,255,255,0.25);
  background: rgba(255,255,255,0.08);
  color: white;
}
.btn-again:hover, .btn-level:hover { transform: translateY(-0.125rem); }
.btn-level:hover { background: rgba(255,255,255,0.16); }

/* ── Track area ── */
.track-area {
  flex: 1 1 0;
  min-height: 0;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  padding: 0.25rem 1rem 0.75rem;
  position: relative;
  z-index: 5;
}

/* ── Individual lane: [label] [road with start → finish] ── */
.lane {
  --car-w: 8.5rem;
  flex: 1;
  min-height: 0;
  display: flex;
  border-radius: 0.875rem;
  overflow: hidden;
  border: 0.0625rem solid var(--team-line);
  background: linear-gradient(90deg, var(--team-soft), rgba(255,255,255,0.02));
}

.lane-tag {
  flex: 0 0 6.5rem;
  display: flex; align-items: center; justify-content: center; gap: 0.5rem;
  border-right: 0.0625rem solid var(--team-line);
  background: var(--team-soft);
  color: var(--team-light);
  font-family: 'Plus Jakarta Sans', 'Fredoka', sans-serif;
  font-size: 0.85rem; font-weight: 800;
  letter-spacing: 0.14em;
  text-transform: uppercase;
}

.road {
  flex: 1;
  position: relative;
  min-width: 0;
}

/* Road dashes along the middle of the lane */
.road-dashes {
  position: absolute; inset: 0;
  display: flex; align-items: center;
  padding: 0 2.5rem 0 1rem;
}
.road-dash {
  flex: 1;
  height: 0.1875rem;
  background: rgba(255,255,255,0.12);
  border-radius: 0.125rem;
  margin: 0 0.375rem;
}

/* Start & finish lines — per lane */
.start-line {
  position: absolute;
  left: 0.5rem; top: 0; bottom: 0;
  width: 0.25rem;
  background: rgba(255,255,255,0.55);
}
.finish-line {
  position: absolute;
  right: 0; top: 0; bottom: 0;
  width: 1.5rem;
  background: repeating-conic-gradient(#111 0 25%, #fff 0 50%) 0 0 / 0.75rem 0.75rem;
}

/* Cars ride between the start line and the finish line */
.car-track {
  position: absolute;
  top: 0; bottom: 0;
  left: 1rem; right: 1.5rem;
}

/* ── Car wrapper ── */
.car-wrapper {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  transition: left 0.35s cubic-bezier(0.22, 1, 0.36, 1);
  width: var(--car-w);
  z-index: 2;
}

.car-wrapper.stunned {
  animation: swerve 0.4s ease-in-out;
}

@keyframes swerve {
  0%   { transform: translateY(-50%) rotate(0deg); }
  25%  { transform: translateY(calc(-50% - 0.625rem)) rotate(-8deg); }
  75%  { transform: translateY(calc(-50% + 0.625rem)) rotate(8deg); }
  100% { transform: translateY(-50%) rotate(0deg); }
}

.car-svg {
  display: block;
  width: 100%;
  height: auto;
  filter: drop-shadow(0 0.375rem 0.5rem rgba(0,0,0,0.6));
  animation: carBounce 0.3s ease-in-out infinite alternate;
}
@keyframes carBounce {
  from { transform: translateY(0); }
  to   { transform: translateY(-0.1875rem); }
}

/* Smoke on accelerate */
.smoke-puff {
  position: absolute;
  right: 90%;
  top: 50%;
  transform: translateY(-50%);
  width: 1.875rem; height: 1.875rem;
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
  flex: 1.1 1 0;
  min-height: 0;
  background: rgba(6,10,22,0.55);
  border-top: 0.0625rem solid rgba(255,255,255,0.08);
  position: relative;
  z-index: 10;
}

/* Slim divider with a VS badge in the middle */
.divider {
  flex: 0 0 3.5rem;
  position: relative;
  display: flex; align-items: center; justify-content: center;
}
.divider::before {
  content: '';
  position: absolute; top: 0; bottom: 0; left: 50%;
  width: 0.0625rem;
  background: rgba(255,255,255,0.1);
}
.divider span {
  position: relative;
  width: 2.75rem; height: 2.75rem;
  border-radius: 50%;
  display: flex; align-items: center; justify-content: center;
  background: #FFC107;
  color: #1a1a1a;
  font-size: 1.05rem; font-weight: 700;
  box-shadow: 0 0 0 0.375rem rgba(255,193,7,0.15);
}

.control-panel {
  flex: 1; padding: 0.75rem 1.125rem 0.875rem;
  display: flex; flex-direction: column; gap: 0.5rem;
  min-width: 0; min-height: 0;
  border-top: 0.25rem solid var(--team);
}

/* Team name + display on one row, keypad gets the rest */
.panel-top {
  display: flex; align-items: center; gap: 1rem;
  flex-shrink: 0;
}
.team-name {
  display: flex; align-items: center; gap: 0.625rem;
  color: white;
  font-size: 1.45rem; font-weight: 700; line-height: 1.2;
  white-space: nowrap;
}

/* Calculator display */
.calc-display {
  flex: 1; min-width: 0;
  background: rgba(0,0,0,0.35);
  border: 0.0625rem solid rgba(255,255,255,0.12);
  border-radius: 0.75rem;
  padding: 0.375rem 1rem;
  display: flex; align-items: center; justify-content: space-between;
  transition: border-color 0.3s, background 0.3s;
}
.calc-display.stunned {
  border-color: rgba(255,82,82,0.7);
  background: rgba(255,82,82,0.1);
}
.calc-expr {
  font-family: 'Plus Jakarta Sans', 'Fredoka', sans-serif;
  font-size: 1.15rem; font-weight: 600;
  color: rgba(255,255,255,0.55);
  white-space: nowrap;
}
.calc-expr.stun-warn {
  color: #FF5252;
  animation: blink 0.4s infinite;
}
@keyframes blink { 0%,100% { opacity:1; } 50% { opacity:0.25; } }
.calc-input {
  font-size: 2.2rem; font-weight: 700; line-height: 1.1;
  color: white;
  letter-spacing: 0.125rem;
}

/* Numpad 3-col grid — 4 baris dibagi rata sisa tinggi panel */
.numpad {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(4, minmax(0, 1fr));
  gap: 0.4rem;
  flex: 1;
  min-height: 0;
}

.numpad-btn {
  border: 0.0625rem solid rgba(255,255,255,0.1);
  border-radius: 0.75rem;
  cursor: pointer;
  font-family: 'Fredoka', sans-serif; font-size: 2rem; font-weight: 700;
  line-height: 1; padding: 0;
  color: white;
  min-height: 0;
  transition: background 0.1s, transform 0.1s;
  touch-action: manipulation;
}
.numpad-btn:active:not(:disabled) { transform: scale(0.95); }
.numpad-btn:disabled { opacity: 0.35; cursor: not-allowed; }

.btn-digit { background: rgba(255,255,255,0.07); }
.btn-digit:hover:not(:disabled) { background: rgba(255,255,255,0.13); }

.btn-del {
  background: rgba(255,255,255,0.03);
  color: rgba(255,255,255,0.7);
  font-size: 1.6rem;
}
.btn-del:hover:not(:disabled) { background: rgba(255,255,255,0.1); }

.btn-submit {
  border-color: transparent;
  background: var(--team);
  font-size: 2.2rem;
  box-shadow: 0 0.375rem 1rem var(--team-soft);
}
.btn-submit:hover:not(:disabled) { filter: brightness(1.08); }

/* ── Countdown Overlay ── */
.countdown-overlay {
  position: fixed; inset: 0; z-index: 200;
  background: rgba(0, 0, 0, 0.78);
  display: flex; flex-direction: column;
  align-items: center; justify-content: center;
  backdrop-filter: blur(0.375rem);
}

.countdown-number {
  font-family: 'Fredoka', sans-serif;
  font-size: 22vw;
  font-weight: 900;
  line-height: 1;
  text-align: center;
  white-space: pre-line;
  text-shadow: 0 0 3.75rem currentColor, 0 0.5rem 1.5rem rgba(0,0,0,0.8);
  filter: drop-shadow(0 0 2.5rem currentColor);
}

.cd-red    { color: #FF5252; }
.cd-yellow { color: #FFC107; }
.cd-green  { color: #69F0AE; }
.cd-go     { font-size: 12vw; color: #FFFFFF; }

.countdown-subtext {
  font-family: 'Fredoka', sans-serif;
  font-size: 1.8rem;
  color: rgba(255,255,255,0.5);
  margin-top: 0.625rem;
  letter-spacing: 0.25rem;
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
