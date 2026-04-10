<script setup>
const props = defineProps({
  questionText: String,
  choices: Array, // Array of numbers
  disabled: Boolean,
  selectedAnswer: Number, // The answer that was click, for highlighting
  correctAnswer: Number  
});

const emit = defineEmits(['answer']);

function selectChoice(choice) {
  if (!props.disabled) {
    emit('answer', choice);
  }
}
</script>

<template>
  <div class="math-problem glass-panel">
    <div class="question-box">
      <h1>{{ questionText }}</h1>
    </div>
    <div class="choices-grid">
      <button 
        v-for="(choice, index) in choices" 
        :key="index"
        class="btn-choice"
        :class="{
          'wrong': disabled && selectedAnswer === choice && choice !== correctAnswer,
          'correct': disabled && choice === correctAnswer && (selectedAnswer === choice || selectedAnswer !== null)
        }"
        :disabled="disabled"
        @click="selectChoice(choice)"
      >
        {{ choice }}
      </button>
    </div>
  </div>
</template>

<style scoped>
.math-problem {
  width: 90%;
  max-width: 800px;
  margin: 0 auto 30px auto;
  padding: 30px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 20px;
}

.question-box {
  background: white;
  padding: 10px 40px;
  border-radius: 20px;
  box-shadow: inset 0 0 10px rgba(0,0,0,0.1);
  color: var(--text-dark);
}

.question-box h1 {
  font-size: 3.5rem;
  margin: 0;
  font-family: 'Fredoka', sans-serif;
}

.choices-grid {
  display: flex;
  gap: 20px;
  flex-wrap: wrap;
  justify-content: center;
  width: 100%;
}

.btn-choice {
  min-width: 100px;
  flex: 1;
  max-width: 150px;
}

/* Disabled styling */
.btn-choice:disabled {
  cursor: not-allowed;
  opacity: 0.8;
}

.btn-choice:disabled:hover {
  transform: none; /* remove hover if disabled */
  box-shadow: 0 6px 0 #2E7D32, 0 10px 20px rgba(0,0,0,0.2); 
}

.btn-choice.wrong:disabled:hover {
  box-shadow: 0 6px 0 #C62828, 0 10px 20px rgba(0,0,0,0.2);
}

.btn-choice.correct:disabled:hover {
  box-shadow: 0 6px 0 #1565C0, 0 10px 20px rgba(0,0,0,0.2);
}
</style>
