<script setup>
import { ref, computed } from 'vue'

// 任务状态
const tasks = ref([])
const newTask = ref('')

// 添加任务
const addTask = () => {
  if (newTask.value.trim() !== '') {
    tasks.value.push({
      id: Date.now(),
      text: newTask.value.trim(),
      completed: false
    })
    newTask.value = ''
  }
}

// 切换任务完成状态
const toggleTask = (id) => {
  const task = tasks.value.find(task => task.id === id)
  if (task) {
    task.completed = !task.completed
  }
}

// 删除任务
const deleteTask = (id) => {
  tasks.value = tasks.value.filter(task => task.id !== id)
}

// 计算未完成任务数量
const pendingTasksCount = computed(() => {
  return tasks.value.filter(task => !task.completed).length
})
</script>

<template>
  <div class="task-manager">
    <h2 class="task-title">任务管理</h2>
    
    <!-- 添加任务 -->
    <div class="add-task">
      <input 
        v-model="newTask" 
        @keyup.enter="addTask"
        placeholder="添加新任务..."
        class="task-input"
      />
      <button @click="addTask" class="add-btn">添加</button>
    </div>
    
    <!-- 任务列表 -->
    <div class="task-list">
      <div v-if="pendingTasksCount > 0" class="task-summary">
        剩余任务: {{ pendingTasksCount }}
      </div>
      
      <div 
        v-for="task in tasks" 
        :key="task.id" 
        class="task-item"
        :class="{ completed: task.completed }"
      >
        <input 
          type="checkbox" 
          :checked="task.completed" 
          @change="toggleTask(task.id)"
          class="task-checkbox"
        />
        <span class="task-text">{{ task.text }}</span>
        <button @click="deleteTask(task.id)" class="delete-btn">删除</button>
      </div>
      
      <div v-if="tasks.length === 0" class="no-tasks">
        暂无任务，添加一个新任务吧！
      </div>
    </div>
  </div>
</template>

<style scoped>
.task-manager {
  margin-top: 30px;
  width: 100%;
}

.task-title {
  color: #333;
  margin-bottom: 20px;
  font-size: 1.5rem;
}

.add-task {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.task-input {
  flex: 1;
  padding: 12px 15px;
  border: 2px solid #e0e0e0;
  border-radius: 30px;
  font-size: 1rem;
  outline: none;
  transition: border-color 0.3s ease;
}

.task-input:focus {
  border-color: #4ecdc4;
}

.add-btn {
  padding: 12px 20px;
  border: none;
  border-radius: 30px;
  background-color: #4ecdc4;
  color: white;
  font-size: 1rem;
  cursor: pointer;
  transition: all 0.3s ease;
}

.add-btn:hover {
  background-color: #3db8af;
  transform: translateY(-2px);
}

.task-summary {
  text-align: left;
  margin-bottom: 15px;
  color: #666;
  font-size: 1rem;
}

.task-item {
  display: flex;
  align-items: center;
  padding: 15px;
  background-color: white;
  border-radius: 10px;
  margin-bottom: 10px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.05);
  transition: all 0.3s ease;
}

.task-item:hover {
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
}

.task-item.completed .task-text {
  text-decoration: line-through;
  color: #999;
}

.task-checkbox {
  margin-right: 15px;
  width: 18px;
  height: 18px;
  cursor: pointer;
}

.task-text {
  flex: 1;
  text-align: left;
  font-size: 1.1rem;
  color: #333;
}

.delete-btn {
  padding: 8px 15px;
  border: none;
  border-radius: 20px;
  background-color: #ff6b6b;
  color: white;
  font-size: 0.9rem;
  cursor: pointer;
  transition: all 0.3s ease;
}

.delete-btn:hover {
  background-color: #e55a5a;
  transform: translateY(-2px);
}

.no-tasks {
  text-align: center;
  color: #999;
  font-style: italic;
  padding: 20px;
}

/* 响应式设计 */
@media (max-width: 600px) {
  .add-task {
    flex-direction: column;
  }
  
  .task-input {
    margin-bottom: 10px;
  }
  
  .task-text {
    font-size: 1rem;
  }
}
</style>