<template>
  <div>
    <h1>To-Do List</h1>
    <input v-model="newTask" @keyup.enter="addTask" placeholder="Add a new task">
    <ul>
      <li v-for="(task, index) in filteredTasks" :key="index" :class="{ 'completed': task.completed }">
        <span @click="toggleTask(index)">{{ task.description }}</span>
        <button @click="deleteTask(index)">Delete</button>
      </li>
    </ul>
    <div class="filter">
      <label>Show only incomplete tasks:</label>
      <input type="checkbox" v-model="showOnlyIncomplete">
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';

interface Task {
  description: string;
  completed: boolean;
}

const tasks = ref<Task[]>([
  { description: 'Example task 1', completed: false },
  { description: 'Example task 2', completed: true },
]);
const newTask = ref('');
const showOnlyIncomplete = ref(false);

const addTask = () => {
  if (newTask.value.trim() !== '') {
    tasks.value.push({ description: newTask.value, completed: false });
    newTask.value = '';
  }
};

const deleteTask = (index: number) => {
  tasks.value.splice(index, 1);
};

const toggleTask = (index: number) => {
  tasks.value[index].completed = !tasks.value[index].completed;
};

const filteredTasks = computed(() => {
  if (showOnlyIncomplete.value) {
    return tasks.value.filter(task => !task.completed);
  } else {
    return tasks.value;
  }
});
</script>

<style scoped>
.completed {
  text-decoration: line-through;
}
.filter {
  margin-top: 10px;
}
</style>
