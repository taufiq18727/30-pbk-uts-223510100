<template>
    <div>
      <h2>To-Do List</h2>
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
      <!-- Slot for additional content -->
      <slot></slot>
    </div>
  </template>
  
  <script setup lang="ts">
  import { ref, computed, defineProps } from 'vue';
  
  interface Task {
    description: string;
    completed: boolean;
  }
  
  const props = defineProps<{
    tasks: Task[];
  }>();
  
  const tasks = ref<Task[]>(props.tasks);
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
  