<template>
  <div class="app-container">
    <form @submit.prevent="save">
      <input type="text" v-model="form.title" placeholder="Enter title" class="input-title"/><br />
      <textarea v-model="form.content" placeholder="Enter content" class="textarea-content"></textarea><br />
      <button type="submit" class="btn-save">Save</button>
    </form>
    <ul class="articles-list">
      <li v-for="article in articles" :key="article.id" class="article-item">
        <h3>{{ article.title }}</h3>
        <p>{{ article.content }}</p>
        <button @click="edit(article)" class="btn-edit">Edit</button>
        <button @click="remove(article.id)" class="btn-delete">Delete</button>
      </li>
    </ul>
    <button @click="load" class="btn-load">Load</button>
  </div>
</template>

<script>
import { ref, onMounted, reactive } from 'vue';
import axios from 'axios';

export default {
  setup() {
    const form = reactive({
      id: null,
      title: '',
      content: ''
    });

    const articles = ref([]);

    async function load() {
      try {
        const response = await axios.get('https://tugas6-prak-pbk-nu.vercel.app/articles');
        articles.value = response.data;
      } catch (error) {
        console.error('Error loading articles:', error);
      }
    }

    async function save() {
      try {
        const url = form.id
          ? `https://tugas6-prak-pbk-nu.vercel.app/articles/${form.id}`
          : 'https://tugas6-prak-pbk-nu.vercel.app/articles';
        const method = form.id ? 'put' : 'post';
        const response = await axios[method](url, form);

        const index = articles.value.findIndex(article => article.id === form.id);
        if (index !== -1) {
          articles.value[index] = response.data;
        } else {
          articles.value.push(response.data);
        }

        form.id = null;
        form.title = '';
        form.content = '';
      } catch (error) {
        console.error("Error saving article:", error);
      }
    }

    async function remove(articleId) {
      try {
        await axios.delete(`https://tugas6-prak-pbk-nu.vercel.app/articles/${articleId}`);
        articles.value = articles.value.filter((article) => article.id !== articleId);
      } catch (error) {
        console.error("Error deleting article:", error);
      }
    }

    function edit(article) {
      form.id = article.id;
      form.title = article.title;
      form.content = article.content;
    }

    onMounted(load);

    return { form, articles, save, edit, remove };
  }
};
</script>

<style scoped>
body {
  background-image: url('https://images.unsplash.com/photo-1497366811353-6870744d04b2?ixlib=rb-1.2.1&auto=format&fit=crop&w=750&q=80');
  background-size: cover;
  background-position: center;
}

.app-container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  background-color: #070707;
  background-image: url('https://images.unsplash.com/photo-1497366811353-6870744d04b2?ixlib=rb-1.2.1&auto=format&fit=crop&w=750&q=80');
  background-size: cover; /* Changed from 'auto' to 'cover' to ensure the background image covers the entire container */
  border-radius: 8px;
}

.input-title, .textarea-content {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.btn-save, .btn-edit, .btn-delete, .btn-load {
  padding: 10px 20px;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.btn-save {
  background-color: #4CAF50;
}

.btn-edit {
  background-color: #2196F3;
}

.btn-delete {
  background-color: #f44336;
}

.btn-load {
  background-color: #e7e7e7;
  color: black;
}

.articles-list {
  list-style-type: none;
  padding: 0;
}

.article-item {
  padding: 10px;
  margin-bottom: 10px;
  background-color: #fff;
  border: 1px solid #ddd;
  border-radius: 4px;
}

.article-item h3 {
  margin-top: 0;
  color: orange; 
}

.article-item p {
  color: orange;
}
</style>
