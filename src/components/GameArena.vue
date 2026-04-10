<template>
  <div class="game-arena">
    <div class="vignette"></div>
    <slot></slot>
  </div>
</template>

<style scoped>
.game-arena {
  width: 100vw;
  height: 100vh;
  position: relative;
  background-image: url('../assets/battle_arena.png');
  background-size: cover;
  background-position: center bottom;
  background-repeat: no-repeat;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.vignette {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: radial-gradient(circle, rgba(0,0,0,0) 40%, rgba(0,0,0,0.6) 100%);
  pointer-events: none;
  z-index: 1;
}

/* Ensure contents of GameArena are above the vignette somewhat */
::v-deep(*) {
  z-index: 2;
  position: relative;
}
</style>
