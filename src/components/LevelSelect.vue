<script setup>
const emit = defineEmits(['select-level', 'back']);

// `emoji` + `name` are also shown on the race screen's level badge
const levels = [
  {
    id: 1,
    name: 'Mudah',
    emoji: '⭐',
    range: '1–5 × 1–5',
    desc: 'Perkalian angka 1 sampai 5. Cocok untuk pemanasan!',
    color: '#4CAF50',
  },
  {
    id: 2,
    name: 'Sedang',
    emoji: '⭐⭐',
    range: '1–7 × 1–7',
    desc: 'Perkalian angka 1 sampai 7. Sudah mulai menantang!',
    color: '#2196F3',
  },
  {
    id: 3,
    name: 'Sulit',
    emoji: '⭐⭐⭐',
    range: '1–9 × 1–9',
    desc: 'Perkalian angka 1 sampai 9. Butuh konsentrasi!',
    color: '#FF9800',
  },
  {
    id: 4,
    name: 'Juara!',
    emoji: '🏆',
    range: '1–10 × 1–10',
    desc: 'Perkalian lengkap 1 sampai 10. Siap jadi juara?',
    color: '#E91E63',
  },
];
</script>

<template>
  <div class="level-select">
    <div class="level-vignette"></div>

    <button class="btn-back" @click="emit('back')">Kembali</button>

    <div class="level-content">
      <div class="level-header">
        <div class="eyebrow">Balap Perkalian</div>
        <h1>Pilih Tingkat Kesulitan</h1>
        <p>Semakin tinggi level, semakin besar angkanya!</p>
      </div>

      <div class="levels-grid">
        <button
          v-for="level in levels"
          :key="level.id"
          class="level-card"
          :style="{ '--accent': level.color }"
          @click="emit('select-level', level)"
        >
          <div class="card-top">
            <span class="level-no">Level {{ level.id }}</span>
            <span class="level-meter" :aria-label="`Kesulitan ${level.id} dari 4`">
              <i v-for="n in 4" :key="n" :class="{ on: n <= level.id }"></i>
            </span>
          </div>

          <h2>{{ level.name }}</h2>

          <div class="range">
            <span class="range-label">Angka</span>
            <span class="range-value">{{ level.range }}</span>
          </div>

          <p>{{ level.desc }}</p>

          <span class="btn-start">Mulai <span class="arrow">→</span></span>
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Same navy backdrop as the main menu */
.level-select {
  width: 100%;
  height: 100%;
  position: relative;
  overflow: hidden;
  background: linear-gradient(135deg, #1a1a2e 0%, #16213e 55%, #0f3460 100%);
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: 'Fredoka', sans-serif;
}

.level-vignette {
  position: absolute; inset: 0;
  background: radial-gradient(ellipse at center, transparent 45%, rgba(0,0,0,0.45) 100%);
  pointer-events: none;
  z-index: 1;
}

.btn-back {
  position: absolute; top: 1.5rem; left: 1.5rem; z-index: 100;
  background: rgba(255, 255, 255, 0.12);
  backdrop-filter: blur(0.75rem);
  border: 0.0625rem solid rgba(255, 255, 255, 0.25);
  color: white; padding: 0.625rem 1.5rem;
  border-radius: 99px;
  font-family: 'Fredoka', sans-serif;
  font-size: 1.1rem; font-weight: 700; cursor: pointer;
  display: flex; align-items: center; gap: 0.625rem;
  transition: background 0.2s, transform 0.2s;
}

.btn-back::before {
  content: '←';
  display: inline-block;
  font-size: 1.4rem;
  transition: transform 0.3s;
}

.btn-back:hover {
  background: rgba(255, 255, 255, 0.22);
  transform: translateY(-0.125rem);
}

.btn-back:hover::before {
  transform: translateX(-0.3125rem);
}

.level-content {
  position: relative;
  z-index: 10;
  width: 92%;
  max-width: 66rem;
  display: flex;
  flex-direction: column;
  gap: 2.25rem;
}

/* ── Header ── */
.level-header {
  text-align: center;
  color: white;
}
.eyebrow {
  font-family: 'Plus Jakarta Sans', 'Fredoka', sans-serif;
  font-size: 0.8rem; font-weight: 700;
  color: #FFC107;
  letter-spacing: 0.16em;
  text-transform: uppercase;
}
.level-header h1 {
  margin: 0.375rem 0 0.25rem;
  font-size: 2.6rem; line-height: 1.1;
}
.level-header p {
  margin: 0;
  font-family: 'Plus Jakarta Sans', 'Fredoka', sans-serif;
  font-size: 1.05rem; font-weight: 500;
  color: rgba(255,255,255,0.65);
}

/* ── Grid of 4 level cards ── */
.levels-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 1.25rem;
}

@media (max-width: 900px) {
  .levels-grid { grid-template-columns: repeat(2, 1fr); }
}

/* Dark glass card; the level colour is only an accent */
.level-card {
  display: flex;
  flex-direction: column;
  align-items: stretch;
  gap: 0.75rem;
  padding: 1.375rem 1.375rem 1.375rem;
  text-align: left;
  color: white;
  font-family: inherit;
  cursor: pointer;
  border-radius: 1.25rem;
  background: rgba(255,255,255,0.05); /* fallback for browsers without color-mix */
  background:
    linear-gradient(180deg, color-mix(in srgb, var(--accent) 16%, transparent), transparent 55%),
    rgba(255,255,255,0.04);
  border: 0.0625rem solid rgba(255,255,255,0.12);
  border-top: 0.25rem solid var(--accent);
  box-shadow: 0 1rem 2.5rem rgba(0,0,0,0.3);
  transition: transform 0.2s, border-color 0.2s, box-shadow 0.2s;
}
.level-card:hover {
  transform: translateY(-0.375rem);
  border-color: color-mix(in srgb, var(--accent) 60%, transparent);
  border-top-color: var(--accent);
  box-shadow: 0 1.25rem 3rem rgba(0,0,0,0.4), 0 0 0 0.0625rem color-mix(in srgb, var(--accent) 40%, transparent);
}
.level-card:active { transform: translateY(-0.125rem); }

.card-top {
  display: flex; align-items: center; justify-content: space-between;
}
.level-no {
  font-family: 'Plus Jakarta Sans', 'Fredoka', sans-serif;
  font-size: 0.72rem; font-weight: 700;
  color: var(--accent);
  letter-spacing: 0.14em;
  text-transform: uppercase;
}
/* Difficulty meter: n of 4 dots filled */
.level-meter { display: flex; gap: 0.25rem; }
.level-meter i {
  width: 0.5rem; height: 0.5rem;
  border-radius: 50%;
  background: rgba(255,255,255,0.18);
}
.level-meter i.on { background: var(--accent); }

.level-card h2 {
  margin: 0;
  font-size: 1.9rem; line-height: 1.1;
}

.range {
  display: flex; flex-direction: column;
  padding: 0.75rem 0;
  border-top: 0.0625rem solid rgba(255,255,255,0.1);
  border-bottom: 0.0625rem solid rgba(255,255,255,0.1);
}
.range-label {
  font-family: 'Plus Jakarta Sans', 'Fredoka', sans-serif;
  font-size: 0.68rem; font-weight: 700;
  color: rgba(255,255,255,0.5);
  letter-spacing: 0.14em;
  text-transform: uppercase;
}
.range-value {
  font-size: 1.6rem; font-weight: 700; line-height: 1.2;
  color: var(--accent);
  white-space: nowrap;
}

.level-card p {
  margin: 0;
  font-family: 'Plus Jakarta Sans', 'Fredoka', sans-serif;
  font-size: 0.88rem; line-height: 1.55;
  color: rgba(255,255,255,0.7);
}

/* Full-width CTA pinned to the bottom so all four line up */
.btn-start {
  margin-top: auto;
  display: flex; align-items: center; justify-content: center; gap: 0.5rem;
  padding: 0.7rem 1rem;
  border-radius: 0.75rem;
  background: var(--accent);
  color: white;
  font-size: 1.1rem; font-weight: 700;
  text-shadow: 0 0.0625rem 0.125rem rgba(0,0,0,0.25);
}
.btn-start .arrow { display: inline-block; transition: transform 0.2s; }
.level-card:hover .btn-start .arrow { transform: translateX(0.25rem); }
</style>
