<template>
  <div>
    <header>
      <h1>Post and Todos</h1>
      <nav>
        <ul>
          <li @click="selectedMenu = 'Todos'">Todos</li>
          <li @click="selectedMenu = 'Post'">Post</li>
        </ul>
      </nav>
    </header>
    <main v-if="selectedMenu === 'Todos'">
      <!-- Tampilan Todos -->
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
    </main>
    <main v-else-if="selectedMenu === 'Post'">
      <!-- Tampilan Postingan -->
      <h2>Postingan</h2>
      <select v-model="selectedUser">
        <option v-for="user in users" :key="user.id" :value="user.id">{{ user.name }}</option>
      </select>
      <div v-if="posts.length">
        <ul>
          <li v-for="post in posts" :key="post.id">
            <h3>{{ post.title }}</h3>
            <p>{{ post.body }}</p>
          </li>
        </ul>
      </div>
      <div v-else>
        <p>Loading...</p>
      </div>
    </main>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, watch } from 'vue';

interface Task {
  description: string;
  completed: boolean;
}

const selectedMenu = ref('Todos');
const tasks = ref<Task[]>([
  { description: 'Example task 1', completed: false },
  { description: 'Example task 2', completed: true },
]);
const newTask = ref('');
const showOnlyIncomplete = ref(false);
const selectedUser = ref<number|null>(null);
const users = ref<any[]>([]);
const posts = ref<any[]>([]);

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

// Ambil data pengguna saat komponen dimuat
onMounted(async () => {
  const response = await fetch('https://jsonplaceholder.typicode.com/users');
  users.value = await response.json();
});

// Ambil data postingan saat pengguna dipilih
watch(selectedUser, async (newValue) => {
  if (newValue) {
    const response = await fetch(`https://jsonplaceholder.typicode.com/posts?userId=${newValue}`);
    posts.value = await response.json();
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
