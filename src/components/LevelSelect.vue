<script setup>
const emit = defineEmits(['select-level', 'back']);

const levels = [
  {
    id: 1,
    name: 'Bintang Satu',
    emoji: '⭐',
    range: '1–5',
    desc: 'Perkalian angka 1 sampai 5. Cocok untuk pemanasan!',
    color: '#4CAF50',
    shadow: '#2E7D32',
    bg: 'linear-gradient(135deg, #4CAF50, #81C784)',
  },
  {
    id: 2,
    name: 'Bintang Dua',
    emoji: '⭐⭐',
    range: '1–10',
    desc: 'Perkalian angka 1 sampai 10. Standar pelajaran SD!',
    color: '#2196F3',
    shadow: '#1565C0',
    bg: 'linear-gradient(135deg, #2196F3, #64B5F6)',
  },
  {
    id: 3,
    name: 'Bintang Tiga',
    emoji: '⭐⭐⭐',
    range: '6–12',
    desc: 'Angka lebih besar, butuh konsentrasi ekstra!',
    color: '#FF9800',
    shadow: '#E65100',
    bg: 'linear-gradient(135deg, #FF9800, #FFB74D)',
  },
  {
    id: 4,
    name: 'Juara!',
    emoji: '🏆',
    range: '7–15',
    desc: 'Level terberat! Hanya untuk para ahli matematika sejati.',
    color: '#E91E63',
    shadow: '#880E4F',
    bg: 'linear-gradient(135deg, #E91E63, #F48FB1)',
  },
];
</script>

<template>
  <div class="level-select">
    <div class="level-vignette"></div>

    <button class="btn-back" @click="emit('back')">← Kembali</button>

    <div class="level-content">
      <div class="level-header">
        <div class="race-icon">🏎️</div>
        <h1>Pilih Tingkat Kesulitan</h1>
        <p>Semakin tinggi level, semakin besar angkanya!</p>
      </div>

      <div class="levels-grid">
        <div
          v-for="level in levels"
          :key="level.id"
          class="level-card"
          :style="{ '--card-bg': level.bg, '--card-shadow': level.shadow }"
          @click="emit('select-level', level)"
        >
          <div class="card-glow"></div>
          <div class="card-body">
            <div class="level-emoji">{{ level.emoji }}</div>
            <h2>{{ level.name }}</h2>
            <div class="range-badge">Angka {{ level.range }}</div>
            <p>{{ level.desc }}</p>
            <button class="btn-start">
              Mulai! <span>→</span>
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.level-select {
  width: 100vw;
  height: 100vh;
  position: relative;
  overflow: hidden;
  background: linear-gradient(135deg, #0f0c29, #302b63, #24243e);
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: 'Fredoka', sans-serif;
}

/* Animated star-like particles in background */
.level-select::before {
  content: '';
  position: absolute;
  inset: 0;
  background-image:
    radial-gradient(1px 1px at 20% 30%, rgba(255,255,255,0.6) 0%, transparent 100%),
    radial-gradient(1px 1px at 60% 70%, rgba(255,255,255,0.4) 0%, transparent 100%),
    radial-gradient(2px 2px at 80% 20%, rgba(255,200,100,0.5) 0%, transparent 100%),
    radial-gradient(1px 1px at 40% 80%, rgba(255,255,255,0.3) 0%, transparent 100%),
    radial-gradient(2px 2px at 10% 60%, rgba(100,200,255,0.4) 0%, transparent 100%);
  pointer-events: none;
}

.level-vignette {
  position: absolute; inset: 0;
  background: radial-gradient(ellipse at center, transparent 40%, rgba(0,0,0,0.7) 100%);
  pointer-events: none;
  z-index: 1;
}

.btn-back {
  position: absolute; top: 20px; left: 20px; z-index: 100;
  background: rgba(255,255,255,0.15);
  border: 2px solid rgba(255,255,255,0.3);
  color: white; padding: 10px 20px;
  border-radius: 20px;
  font-family: 'Fredoka', sans-serif;
  font-weight: bold; cursor: pointer;
  backdrop-filter: blur(10px);
  transition: all 0.2s;
}
.btn-back:hover { background: rgba(255,255,255,0.25); transform: translateY(-2px); }

.level-content {
  position: relative;
  z-index: 10;
  width: 95%;
  max-width: 1100px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 30px;
}

.level-header {
  text-align: center;
  color: white;
}

.race-icon {
  font-size: 4rem;
  animation: bounce 1.5s ease-in-out infinite alternate;
}
@keyframes bounce { from { transform: translateY(0); } to { transform: translateY(-12px); } }

.level-header h1 {
  margin: 8px 0 4px;
  font-size: 2.8rem;
  text-shadow: 0 4px 12px rgba(0,0,0,0.5);
}
.level-header p {
  color: rgba(255,255,255,0.7);
  font-size: 1.2rem;
  margin: 0;
}

/* ── Grid of 4 level cards ── */
.levels-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 20px;
  width: 100%;
}

@media (max-width: 900px) {
  .levels-grid { grid-template-columns: repeat(2, 1fr); }
}

.level-card {
  background: var(--card-bg);
  border-radius: 24px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
  transition: transform 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275),
              box-shadow 0.3s ease;
  box-shadow: 0 8px 0 var(--card-shadow), 0 12px 30px rgba(0,0,0,0.4);
}
.level-card:hover {
  transform: translateY(-10px) scale(1.03);
  box-shadow: 0 18px 0 var(--card-shadow), 0 24px 40px rgba(0,0,0,0.5);
}
.level-card:active {
  transform: translateY(4px);
  box-shadow: 0 2px 0 var(--card-shadow), 0 6px 12px rgba(0,0,0,0.3);
}

/* Shine overlay on card */
.card-glow {
  position: absolute;
  inset: 0;
  background: linear-gradient(135deg, rgba(255,255,255,0.2) 0%, transparent 60%);
  pointer-events: none;
  border-radius: 24px;
}

.card-body {
  padding: 28px 20px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
  text-align: center;
  color: white;
}

.level-emoji {
  font-size: 2.8rem;
  filter: drop-shadow(0 4px 6px rgba(0,0,0,0.4));
  animation: floatEmoji 2.5s ease-in-out infinite alternate;
}
@keyframes floatEmoji { from { transform: translateY(0); } to { transform: translateY(-8px); } }

.level-card:nth-child(2) .level-emoji { animation-delay: 0.3s; }
.level-card:nth-child(3) .level-emoji { animation-delay: 0.6s; }
.level-card:nth-child(4) .level-emoji { animation-delay: 0.9s; }

.card-body h2 {
  margin: 0;
  font-size: 1.5rem;
  text-shadow: 0 2px 6px rgba(0,0,0,0.3);
}

.range-badge {
  background: rgba(0,0,0,0.25);
  padding: 4px 18px;
  border-radius: 20px;
  font-size: 1.2rem;
  font-weight: bold;
  border: 2px solid rgba(255,255,255,0.3);
}

.card-body p {
  font-size: 1rem;
  opacity: 0.9;
  margin: 0;
  line-height: 1.4;
}

.btn-start {
  margin-top: 6px;
  background: rgba(255,255,255,0.2);
  border: 2px solid rgba(255,255,255,0.5);
  color: white;
  padding: 10px 28px;
  border-radius: 999px;
  font-family: 'Fredoka', sans-serif;
  font-size: 1.2rem;
  font-weight: bold;
  cursor: pointer;
  transition: all 0.2s;
  backdrop-filter: blur(6px);
}
.btn-start:hover {
  background: rgba(255,255,255,0.35);
  transform: scale(1.05);
}
.btn-start span {
  display: inline-block;
  transition: transform 0.2s;
}
.btn-start:hover span { transform: translateX(4px); }
</style>
