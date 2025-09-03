<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'
import TaskManager from './TaskManager.vue'

// 计时模式
const MODES = {
  WORK: { name: '工作', minutes: 25 },
  SHORT_BREAK: { name: '短休息', minutes: 5 },
  LONG_BREAK: { name: '长休息', minutes: 15 }
}

// 状态
const currentMode = ref(MODES.WORK)
const timeLeft = ref(MODES.WORK.minutes * 60) // 以秒为单位
const isRunning = ref(false)
const pomodorosCompleted = ref(0)
const intervalId = ref(null)

// 页面可见性相关状态
const lastUpdateTime = ref(null)

// 计算显示时间
const displayTime = computed(() => {
  const minutes = Math.floor(timeLeft.value / 60)
  const seconds = timeLeft.value % 60
  return `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`
})

// 切换模式
const switchMode = (mode) => {
  currentMode.value = mode
  timeLeft.value = mode.minutes * 60
  isRunning.value = false
  if (intervalId.value) {
    clearInterval(intervalId.value)
    intervalId.value = null
  }
}

// 开始/暂停计时
const toggleTimer = () => {
  isRunning.value = !isRunning.value
  
  if (isRunning.value) {
    // 记录开始时间
    lastUpdateTime.value = Date.now()
    
    // 开始计时
    intervalId.value = setInterval(() => {
      if (timeLeft.value > 0) {
        timeLeft.value--
        lastUpdateTime.value = Date.now()
      } else {
        // 计时结束
        handleTimerEnd()
      }
    }, 1000)
  } else {
    // 暂停计时
    if (intervalId.value) {
      clearInterval(intervalId.value)
      intervalId.value = null
    }
  }
}

// 重置计时
const resetTimer = () => {
  timeLeft.value = currentMode.value.minutes * 60
  isRunning.value = false
  if (intervalId.value) {
    clearInterval(intervalId.value)
    intervalId.value = null
  }
}

// 处理计时结束
const handleTimerEnd = () => {
  // 播放提示音
  playNotificationSound()
  
  // 停止计时器
  if (intervalId.value) {
    clearInterval(intervalId.value)
    intervalId.value = null
  }
  
  isRunning.value = false
  
  // 更新完成的番茄钟数量
  if (currentMode.value === MODES.WORK) {
    pomodorosCompleted.value++
    
    // 每完成4个番茄钟后进入长休息，否则进入短休息
    if (pomodorosCompleted.value % 4 === 0) {
      switchMode(MODES.LONG_BREAK)
    } else {
      switchMode(MODES.SHORT_BREAK)
    }
  } else {
    // 休息结束后进入工作模式
    switchMode(MODES.WORK)
  }
}

// 播放提示音
const playNotificationSound = () => {
  try {
    // 创建一个简单的 beep 声音
    const context = new (window.AudioContext || window.webkitAudioContext)()
    const oscillator = context.createOscillator()
    const gainNode = context.createGain()
    
    oscillator.connect(gainNode)
    gainNode.connect(context.destination)
    
    oscillator.type = 'sine'
    oscillator.frequency.value = 800
    gainNode.gain.value = 0.3
    
    oscillator.start()
    setTimeout(() => {
      oscillator.stop()
    }, 1000)
  } catch (error) {
    console.log('无法播放提示音:', error)
  }
}

// 页面可见性变化处理
const handleVisibilityChange = () => {
  if (isRunning.value && document.visibilityState === 'hidden') {
    // 页面隐藏时，记录当前时间
    lastUpdateTime.value = Date.now()
  } else if (isRunning.value && document.visibilityState === 'visible') {
    // 页面重新可见时，计算离线时间并更新计时器
    if (lastUpdateTime.value) {
      const offlineTime = Math.floor((Date.now() - lastUpdateTime.value) / 1000)
      if (offlineTime > 0) {
        // 如果离线时间超过了剩余时间，则计时结束
        if (offlineTime >= timeLeft.value) {
          timeLeft.value = 0
          handleTimerEnd()
        } else {
          // 否则减去离线时间
          timeLeft.value -= offlineTime
        }
      }
    }
  }
}

// 组件挂载时添加事件监听器
onMounted(() => {
  document.addEventListener('visibilitychange', handleVisibilityChange)
})

// 组件卸载时清理定时器和事件监听器
onUnmounted(() => {
  if (intervalId.value) {
    clearInterval(intervalId.value)
  }
  document.removeEventListener('visibilitychange', handleVisibilityChange)
})
</script>

<template>
  <div class="pomodoro-container">
    <!-- 模式选择按钮 -->
    <div class="mode-selector">
      <button 
        :class="{ active: currentMode === MODES.WORK }"
        @click="switchMode(MODES.WORK)"
      >
        工作 ({{ MODES.WORK.minutes }}分钟)
      </button>
      <button 
        :class="{ active: currentMode === MODES.SHORT_BREAK }"
        @click="switchMode(MODES.SHORT_BREAK)"
      >
        短休息 ({{ MODES.SHORT_BREAK.minutes }}分钟)
      </button>
      <button 
        :class="{ active: currentMode === MODES.LONG_BREAK }"
        @click="switchMode(MODES.LONG_BREAK)"
      >
        长休息 ({{ MODES.LONG_BREAK.minutes }}分钟)
      </button>
    </div>
    
    <!-- 计时器显示 -->
    <div class="timer-display">
      <div class="mode-name">{{ currentMode.name }}</div>
      <div class="time-display">{{ displayTime }}</div>
      <div class="pomodoro-count">已完成: {{ pomodorosCompleted }} 个番茄钟</div>
    </div>
    
    <!-- 控制按钮 -->
    <div class="controls">
      <button @click="toggleTimer" class="control-btn">
        {{ isRunning ? '暂停' : '开始' }}
      </button>
      <button @click="resetTimer" class="control-btn reset-btn">
        重置
      </button>
    </div>
    
    <!-- 任务管理 -->
    <TaskManager />
    
    <!-- 音频元素 -->
    <!-- <audio ref="audio" src="/notification.mp3"></audio> -->
  </div>
</template>

<style scoped>
.pomodoro-container {
  background-color: #f5f5f5;
  border-radius: 15px;
  padding: 30px;
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
  text-align: center;
  max-width: 500px;
  width: 100%;
}

.mode-selector {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-bottom: 30px;
  flex-wrap: wrap;
}

.mode-selector button {
  padding: 10px 15px;
  border: none;
  border-radius: 20px;
  background-color: #e0e0e0;
  color: #333;
  cursor: pointer;
  transition: all 0.3s ease;
  font-size: 0.9rem;
}

.mode-selector button:hover {
  background-color: #d5d5d5;
}

.mode-selector button.active {
  background-color: #ff6b6b;
  color: white;
}

.timer-display {
  margin: 30px 0;
}

.mode-name {
  font-size: 1.5rem;
  color: #333;
  margin-bottom: 10px;
}

.time-display {
  font-size: 4rem;
  font-weight: bold;
  color: #333;
  font-family: 'Courier New', monospace;
  margin: 10px 0;
}

.pomodoro-count {
  font-size: 1.1rem;
  color: #666;
  margin-top: 10px;
}

.controls {
  display: flex;
  justify-content: center;
  gap: 20px;
  margin-bottom: 30px;
}

.control-btn {
  padding: 12px 30px;
  border: none;
  border-radius: 30px;
  background-color: #4ecdc4;
  color: white;
  font-size: 1.1rem;
  cursor: pointer;
  transition: all 0.3s ease;
}

.control-btn:hover {
  background-color: #3db8af;
  transform: translateY(-2px);
}

.reset-btn {
  background-color: #ff6b6b;
}

.reset-btn:hover {
  background-color: #e55a5a;
}

/* 响应式设计 */
@media (max-width: 600px) {
  .pomodoro-container {
    padding: 20px;
  }
  
  .mode-selector {
    gap: 5px;
  }
  
  .mode-selector button {
    padding: 8px 12px;
    font-size: 0.8rem;
  }
  
  .time-display {
    font-size: 3rem;
  }
  
  .control-btn {
    padding: 10px 20px;
    font-size: 1rem;
  }
}
</style>