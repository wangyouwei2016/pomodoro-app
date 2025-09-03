<script setup>
import PomodoroTimer from './components/PomodoroTimer.vue'
</script>

<template>
  <div class="app-container">
    <h1 class="app-title">番茄钟</h1>
    <PomodoroTimer />
  </div>
</template>

<style scoped>
.app-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  padding: 20px;
  font-family: 'Arial', sans-serif;
}

.app-title {
  color: #333;
  margin-bottom: 30px;
  font-size: 2.5rem;
  text-align: center;
}
</style>