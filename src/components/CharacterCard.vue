<script setup>
import { computed } from 'vue';
import impactImage from '../assets/impact_hit.png';

const props = defineProps({
  name: String,
  image: String,
  maxHealth: { type: Number, default: 100 },
  currentHealth: { type: Number, default: 100 },
  animState: { type: String, default: 'idle' }, // 'idle', 'attack', 'hurt', 'defeat'
  isMonster: { type: Boolean, default: false }
});

const healthPercentage = computed(() => {
  return Math.max(0, (props.currentHealth / props.maxHealth) * 100);
});

const healthColor = computed(() => {
  if (healthPercentage.value > 50) return '#4CAF50'; 
  if (healthPercentage.value > 20) return '#FFC107'; 
  return '#F44336'; 
});

const characterClasses = computed(() => {
  return {
    'anim-float': props.animState === 'idle' && !props.isMonster,
    'anim-float-delay': props.animState === 'idle' && props.isMonster,
    'anim-attack-hero': props.animState === 'attack' && !props.isMonster,
    'anim-attack-monster': props.animState === 'attack' && props.isMonster,
    'anim-shake': props.animState === 'hurt',
    'anim-defeat': props.animState === 'defeat',
  };
});
</script>

<template>
  <div class="character-card">
    <div class="hud glass-panel-dark">
      <h2>{{ name }}</h2>
      <div class="health-bar-container">
        <div 
          class="health-bar-fill" 
          :style="{ width: healthPercentage + '%', backgroundColor: healthColor }"
        ></div>
        <div class="health-text">{{ Math.max(0, currentHealth) }} / {{ maxHealth }}</div>
      </div>
    </div>
    
    <div class="character-sprite" :class="characterClasses">
      <img :src="image" :alt="name" class="sprite-image" :class="{'flip-x': isMonster}" />
      
      <!-- HIT EFFECT OVERLAY -->
      <transition name="hit-flash">
        <div v-if="animState === 'hurt'" class="impact-effect">
          <img :src="impactImage" alt="Hit!" class="impact-img" />
          <span class="floating-text">-20</span>
        </div>
      </transition>

    </div>
  </div>
</template>

<style scoped>
.character-card {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 40%;
  max-width: 400px;
}

.hud {
  width: 100%;
  padding: 15px;
  text-align: center;
  margin-bottom: 20px;
}

.hud h2 {
  margin: 0 0 10px 0;
  font-family: 'Fredoka', sans-serif;
  font-size: 1.8rem;
  letter-spacing: 1px;
}

.health-bar-container {
  width: 100%;
  height: 30px;
  background-color: rgba(255, 255, 255, 0.2);
  border-radius: 15px;
  overflow: hidden;
  position: relative;
  border: 2px solid rgba(255,255,255,0.4);
}

.health-bar-fill {
  height: 100%;
  border-radius: 13px;
  transition: width 0.4s ease-out, background-color 0.4s ease-out;
}

.health-text {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
  font-size: 1.1rem;
  font-family: monospace;
  text-shadow: 1px 1px 2px black;
}

.character-sprite {
  height: 40vh; 
  min-height: 250px;
  display: flex;
  align-items: flex-end;
  justify-content: center;
  position: relative; /* relative for absolute hit effect */
}

.sprite-image {
  max-height: 100%;
  max-width: 100%;
  object-fit: contain;
  filter: drop-shadow(0 20px 10px rgba(0,0,0,0.5));
}

.flip-x {
  transform: scaleX(-1);
}

/* EFFECTS */
.impact-effect {
  position: absolute;
  top: 30%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 150px;
  height: 150px;
  display: flex;
  justify-content: center;
  align-items: center;
  pointer-events: none;
  z-index: 20;
}

.impact-img {
  width: 100%;
  animation: explode 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
}

.floating-text {
  position: absolute;
  font-size: 4rem;
  font-weight: 900;
  color: #FF1744;
  -webkit-text-stroke: 2px white;
  text-shadow: 0 5px 10px rgba(0,0,0,0.5);
  font-family: 'Fredoka', cursive;
  animation: floatUp 0.8s ease-out forwards;
}

@keyframes explode {
  0% { transform: scale(0) rotate(-45deg); opacity: 0; }
  50% { transform: scale(1.2) rotate(10deg); opacity: 1; }
  100% { transform: scale(1) rotate(0deg); opacity: 0; }
}

@keyframes floatUp {
  0% { transform: translateY(20px) scale(0.5); opacity: 0; }
  20% { transform: translateY(0) scale(1.2); opacity: 1; }
  100% { transform: translateY(-80px) scale(1); opacity: 0; }
}

.hit-flash-enter-active {
  transition: all 0.3s ease;
}
.hit-flash-leave-active {
  transition: all 0.2s ease;
}
</style>
