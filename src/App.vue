<template>
  <q-layout view="lHh Lpr lFf">
    <q-header class="bg-grey-9 text-white">
      <q-toolbar>
        <q-toolbar-title>Tugas PBK</q-toolbar-title>
      </q-toolbar>
    </q-header>
    <q-page-container>
      <q-page>
        <q-page-container class="q-pa-md">
          <q-card class="q-mb-md">
            <q-card-section>
              <div class="text-h6">API</div>
            </q-card-section>
            <q-separator />
            <q-card-section>
              <q-form @submit.prevent="save">
                <q-input v-model="form.title" label="Masukkan Nama" outlined class="q-mb-md" />
                <q-input v-model="form.content" label="Masukkan kata Yang Anda Mau" type="textarea" outlined class="q-mb-md" />
                <div class="q-gutter-sm">
                  <q-btn type="submit" color="primary" label="Simpan" />
                </div>
              </q-form>
            </q-card-section>
          </q-card>

          <q-list bordered separator>
            <q-item v-for="article in articles" :key="article.id" clickable>
              <q-item-section>
                <q-item-label>{{ article.title }}</q-item-label>
                <q-item-label caption>{{ article.content }}</q-item-label>
              </q-item-section>
              <q-item-section side>
                <q-btn flat icon="edit" @click.stop="edit(article)" />
                <q-btn flat icon="delete" color="negative" @click.stop="remove(article.id)" />
              </q-item-section>
            </q-item>
          </q-list>

          <q-btn @click="load" color="secondary" label="Muat Artikel" class="q-mt-md" />
        </q-page-container>

        <q-footer class="bg-grey-9 text-white q-pa-md text-center">
          <q-space />
          <div>
            <p class="text-caption">&copy; @Chikal Verguson-223510295</p>
          </div>
        </q-footer>
      </q-page>
    </q-page-container>
  </q-layout>
</template>

<script setup>
import { reactive, ref } from 'vue';
import axios from 'axios';

const form = reactive({
  id: null,
  title: '',
  content: '',
});

const articles = ref([]);
const apiKey = '$2a$10$v7HrrLX3nWpBu6NLTp103.e6O3RCGn1qK4JhShgsiCkmQcLFsaTd6';  // Ganti dengan API key JSONBin Anda
const binId = '6659e6dfad19ca34f87237f4';  // Ganti dengan bin ID JSONBin Anda
const apiUrl = `https://api.jsonbin.io/v3/b/${binId}`;

async function load() {
  try {
    const response = await axios.get(apiUrl, {
      headers: {
        'X-Master-Key': apiKey
      }
    });
    articles.value = response.data.record.articles; // Assuming articles are stored in `record.articles`
  } catch (error) {
    console.error('Error loading articles', error);
  }
}

async function save() {
  try {
    let updatedArticles;
    if (form.id) {
      updatedArticles = articles.value.map(article => 
        article.id === form.id ? { ...article, ...form } : article
      );
    } else {
      form.id = Date.now().toString();
      updatedArticles = [...articles.value, form];
    }
    
    await axios.put(apiUrl, { articles: updatedArticles }, {
      headers: {
        'Content-Type': 'application/json',
        'X-Master-Key': apiKey
      }
    });
    load();
    resetForm();
  } catch (error) {
    console.error('Error saving article', error);
  }
}

async function remove(id) {
  try {
    const updatedArticles = articles.value.filter(article => article.id !== id);
    await axios.put(apiUrl, { articles: updatedArticles }, {
      headers: {
        'Content-Type': 'application/json',
        'X-Master-Key': apiKey
      }
    });
    load();
  } catch (error) {
    console.error('Error deleting article', error);
  }
}

function edit(article) {
  form.id = article.id;
  form.title = article.title;
  form.content = article.content;
}

function resetForm() {
  form.id = null;
  form.title = '';
  form.content = '';
}
</script>

<style>
/* Your styles here */
</style>
