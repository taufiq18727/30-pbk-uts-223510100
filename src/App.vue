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
      <Todos :tasks="tasks">
        <!-- Additional content for Todos component -->
        <p>Total tasks: {{ tasks.length }}</p>
      </Todos>
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
import { ref, onMounted, watch } from 'vue';
import Todos from './components/todos.vue';

interface Task {
  description: string;
  completed: boolean;
}

const selectedMenu = ref('Todos');
const selectedUser = ref<number | null>(null);
const users = ref<any[]>([]);
const posts = ref<any[]>([]);
const tasks = ref<Task[]>([
  { description: 'Example task 1', completed: false },
  { description: 'Example task 2', completed: true },
]);

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
nav ul {
  list-style-type: none;
  padding: 0;
}

nav li {
  display: inline;
  margin-right: 10px;
  cursor: pointer;
  text-decoration: underline;
}
</style>
