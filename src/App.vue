<script setup>
import { computed, onMounted, ref, useTemplateRef, watch } from 'vue'

const tasks = ref(JSON.parse(localStorage.getItem('tasks')) || [])
const task = ref('')
const taskSelect = ref('')
const search = ref('')
const input = useTemplateRef('my-input')

const addTask = () => {
  if (task.value.trim() !== '' && taskSelect.value.trim() !== '') {
    const newTask = {
      id: tasks?.value?.length + 1,
      description: task.value,
      priority: taskSelect.value,
      isDone: false,
    }
    tasks.value.unshift(newTask)
    task.value = ''
    taskSelect.value = ''
  }
}

watch(
  tasks,
  () => {
    localStorage.setItem('tasks', JSON.stringify(tasks.value))
  },
  { deep: true },
)

const removeItem = (id) => {
  tasks.value = tasks.value.filter((item) => item?.id !== id)
}

const filteredData = computed(() => {
  if (search.value.trim() !== '') {
    return tasks?.value?.filter((item) => item?.description?.includes(search.value))
  }
  return tasks.value
})

onMounted(() => {
  input.value.focus()
})
</script>
<template>
  <div id="app">
    <form @submit.prevent="addTask">
      <!-- Adding tasks form -->
      <input placeholder="Add new task" ref="my-input" v-model="task" />
      <select v-model="taskSelect">
        <option disabled value="">Select priority</option>
        <option value="high">High</option>
        <option value="medium">Medium</option>
        <option value="low">Low</option>
      </select>
      <button type="submit">Add Task</button>
    </form>

    <!-- Task filtering -->
    <input v-model="search" placeholder="Filter tasks..." />

    <!-- If there no tasks -->
    <div v-if="tasks.length === 0">
      <p>No tasks found. Try changing the filter or add some tasks!</p>
    </div>

    <div v-else>
      <h3>Your Tasks</h3>
      <!-- This is a list of tasks -->
      <div
        v-for="task in filteredData"
        :key="task?.id"
        :class="{ task: true, completed: task.isDone, [task.priority.toLowerCase()]: true }"
      >
        <div>
          <!-- Is task done? -->
          <input type="checkbox" v-model="task.isDone" />
          <!-- Task description -->
          <span>{{ task?.description }}</span>
        </div>
        <!-- Removing a task -->
        <button class="remove-button" @click="removeItem(task?.id)">✖</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
body {
  font-family: 'Arial', sans-serif;
  padding: 20px;
  font-size: 16px;
  background-color: #f4f4f9;
}

.task {
  display: flex;
  align-items: center;
  justify-content: space-between;
  background: white;
  padding: 10px 20px;
  margin-bottom: 10px;
  border-radius: 5px;
  border-left: 5px solid;
}

.high {
  border-color: red;
}

.medium {
  border-color: orange;
}

.low {
  border-color: green;
}

.completed {
  text-decoration: line-through;
  color: #bbb;
}

.remove-button {
  color: red;
  cursor: pointer;
  border: none;
  background: none;
  font-size: 16px;
}
</style>
