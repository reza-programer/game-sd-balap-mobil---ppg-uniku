<script setup>
import { ref } from 'vue';
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
  </div>
</template>

<style>
.app-container {
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  background-color: #222;
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
