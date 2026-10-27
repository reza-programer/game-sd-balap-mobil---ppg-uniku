<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import MainMenu    from './components/MainMenu.vue';
import LevelSelect from './components/LevelSelect.vue';
import CarRaceGame from './components/CarRaceGame.vue';

// 'menu' | 'levelselect' | 'race'
const currentView   = ref('menu');
const selectedLevel = ref(null);

function goToLevelSelect() {
  currentView.value = 'levelselect';
}

function startRace(level) {
  selectedLevel.value = level;
  currentView.value   = 'race';
}

function goToMenu() {
  currentView.value = 'menu';
}

function goBackFromRace() {
  currentView.value = 'levelselect'; // back to level select, not main menu
}

// ─── Layar penuh (biar toolbar browser tidak makan tempat di TV) ───
const canFullscreen = !!document.fullscreenEnabled;
const isFullscreen  = ref(!!document.fullscreenElement);

function syncFullscreen() {
  isFullscreen.value = !!document.fullscreenElement;
}

function toggleFullscreen() {
  if (document.fullscreenElement) document.exitFullscreen();
  else document.documentElement.requestFullscreen().catch(() => {});
}

onMounted(() => document.addEventListener('fullscreenchange', syncFullscreen));
onBeforeUnmount(() => document.removeEventListener('fullscreenchange', syncFullscreen));
</script>

<template>
  <div class="app-container">
    <transition name="fade" mode="out-in">

      <MainMenu
        v-if="currentView === 'menu'"
        @start-game="goToLevelSelect"
        key="menu"
      />

      <LevelSelect
        v-else-if="currentView === 'levelselect'"
        @select-level="startRace"
        @back="goToMenu"
        key="levelselect"
      />

      <CarRaceGame
        v-else-if="currentView === 'race'"
        :level="selectedLevel"
        @back="goBackFromRace"
        key="race"
      />

    </transition>

    <button
      v-if="canFullscreen && currentView !== 'race'"
      class="btn-fullscreen"
      @click="toggleFullscreen"
    >
      <svg viewBox="0 0 24 24" aria-hidden="true">
        <path v-if="!isFullscreen" d="M4 9V4h5M20 9V4h-5M4 15v5h5M20 15v5h-5" />
        <path v-else d="M9 4v5H4M15 4v5h5M9 20v-5H4M15 20v-5h5" />
      </svg>
      {{ isFullscreen ? 'Keluar Layar Penuh' : 'Layar Penuh' }}
    </button>
  </div>
</template>

<style>
.app-container {
  width: 100%;
  height: 100vh;
  height: 100dvh;
  overflow: hidden;
  background-color: #222;
  position: relative;
}

.btn-fullscreen {
  position: absolute; top: 1.5rem; right: 1.5rem; z-index: 150;
  display: flex; align-items: center; gap: 0.5rem;
  background: rgba(255, 255, 255, 0.12);
  backdrop-filter: blur(0.75rem);
  border: 1px solid rgba(255, 255, 255, 0.25);
  color: white;
  padding: 0.625rem 1.5rem;
  border-radius: 99px;
  font-family: 'Fredoka', sans-serif;
  font-size: 1.1rem; font-weight: 700;
  cursor: pointer;
  transition: background 0.2s, transform 0.2s;
}
.btn-fullscreen:hover {
  background: rgba(255, 255, 255, 0.22);
  transform: translateY(-2px);
}
.btn-fullscreen svg {
  /* 1.65rem tall = the Kembali text's line height, so both pills are the same height */
  width: 1.3rem; height: 1.65rem;
  fill: none; stroke: currentColor; stroke-width: 2.5;
  stroke-linecap: round; stroke-linejoin: round;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.4s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
