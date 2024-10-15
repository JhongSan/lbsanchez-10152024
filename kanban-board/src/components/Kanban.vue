<template>
  <div>
    <h2>Kanban Board</h2>
    <div class="task-form">
          <input type="text" v-model="taskName" max="250" placeholder="Task Name" class="task-input" />
          <textarea v-model="taskDescription" maxlength="1000" placeholder="Task Description" class="task-textarea"></textarea>
          <select v-model="selectedStatus" class="task-select">
              <option v-for="status in taskStatus" :key="status" :value="status">{{ status }}</option>
          </select>
          <button @click="editMode ? updateTask() : addTask()" class="task-button">
              {{ editMode ? "Update Task" : "Add Task" }}
          </button>
      </div>
      <div class="task-columns">
          <div class="task-column">
              <h2>To Do</h2>
              <div v-for="task in filterStatus('To Do')" :key="task.id" class="task-card">
                  <h3>{{ task.name }}</h3>
                  <p>{{ task.description }}</p>
                  <button @click="editTask(task)" class="edit-button">Edit</button>
              </div>
          </div>
          <div class="task-column">
              <h2>In Progress</h2>
              <div v-for="task in filterStatus('In Progress')" :key="task.id" class="task-card">
                  <h3>{{ task.name }}</h3>
                  <p>{{ task.description }}</p>
                  <button @click="editTask(task)" class="edit-button">Edit</button>
              </div>
          </div>
          <div class="task-column">
              <h2>Done</h2>
              <div v-for="task in filterStatus('Done')" :key="task.id" class="task-card">
                  <h3>{{ task.name }}</h3>
                  <p>{{ task.description }}</p>
                  <button @click="editTask(task)" class="edit-button">Edit</button>
              </div>
          </div>
      </div>
  </div>
</template>

<script lang="ts" setup>
  import {ref} from 'vue'
  import type {Task} from '../types/Task'

  const tasks = ref<Task[]>([])
  const taskName = ref<string>('')
  const taskDescription = ref<string>('')
  const selectedStatus = ref<string>('To Do')
  const taskStatus = ['To Do', 'In Progress', 'Done']
  const editMode = ref<boolean>(false)
  const editingTaskId = ref<number | null>(null)

  const addTask = () => {
    const newTask: Task = {
      id: Date.now(),
      name: taskName.value,
      description: taskDescription.value,
      status: selectedStatus.value
    }

    tasks.value.push(newTask)
    resetForm()
  }

  const filterStatus = (status: string) => tasks.value.filter(task => task.status === status)

  const editTask = (task: Task) => {
    taskName.value = task.name
    taskDescription.value = task.description || ''
    selectedStatus.value = task.status
    editingTaskId.value = task.id
    editMode.value = true
  }

  const updateTask = () => {
    if (editingTaskId.value !== null) {
      const updatedTask = {
        id: editingTaskId.value,
        name: taskName.value,
        description: taskDescription.value,
        status: selectedStatus.value
      }
      const edittedTask = tasks.value.find(t => t.id === editingTaskId.value)
      if(edittedTask){
        edittedTask.name = updatedTask.name
        edittedTask.description = updatedTask.description
        edittedTask.status = updatedTask.status
      }
    }
    resetForm()
  }

  const resetForm = () => {
    taskName.value = ''
    taskDescription.value = ''
    selectedStatus.value = 'To Do'
    editingTaskId.value = null
    editMode.value = false
  }

</script>

<style scoped>
.kanban-board {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
}

.task-form {
  margin-bottom: 20px;
}

.task-input, .task-textarea, .task-select {
  display: block;
  margin-bottom: 10px;
  padding: 10px;
  width: 300px;
}

.task-button {
  background-color: #4CAF50;
  color: white;
  border: none;
  padding: 10px 20px;
  cursor: pointer;
}

.task-button:hover {
  background-color: #45a049;
}

.task-columns {
  display: flex;
  justify-content: space-between;
  width: 100%;
}

.task-column {
  width: 30%;
  border: 1px solid #ddd;
  border-radius: 5px;
  padding: 10px;
}

.task-card {
  border: 1px solid #ddd;
  padding: 10px;
  margin-bottom: 10px;
  border-radius: 5px;
  background-color: #f9f9f9;
}

.edit-button {
  background-color: #f0ad4e;
  color: white;
  border: none;
  padding: 5px 10px;
  cursor: pointer;
  margin-right: 5px;
}

.edit-button:hover {
  background-color: #ec971f;
}
</style>

