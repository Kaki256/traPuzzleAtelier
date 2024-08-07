<script setup lang="ts">
import Home from '../components/Home.vue'
import { ref } from 'vue'

interface Post {
  title: string
  imageBase64: string
  tag: string[]
}

const posts = ref<Post[]>([])

const tags = ['ピースあり', 'ピース回転あり', 'ピース再配置あり', 'ゴール複数あり', 'トーラスあり', 'ワープあり', 'ワープ1つの時に死ぬ', '碇あり']

const newPostTitle = ref('')
const newPostImage = ref<File | null>(null)
const selectedTags = ref<string[]>([])

const onFileChange = (e: Event) => {
  const input = e.target as HTMLInputElement
  if (input.files && input.files[0]) {
    newPostImage.value = input.files[0]
  }
}

const toBase64 = (file: File) => {
  return new Promise<string>((resolve, reject) => {
    const reader = new FileReader();
    reader.readAsDataURL(file);
    reader.onload = () => resolve(reader.result as string);
    reader.onerror = (error) => reject(error);
  });
};

const addPost = async () => {
  if (!newPostTitle.value || !newPostImage.value || selectedTags.value.length === 0) return
  const imageBase64 = await toBase64(newPostImage.value)
  posts.value.push({
    title: newPostTitle.value,
    imageBase64: imageBase64,
    tag: selectedTags.value
  })
  newPostTitle.value = ''
  newPostImage.value = null
  selectedTags.value = []
}

</script>

<template>
  <div class="home">
    <Home />
    <ul>
      <li v-for="post in posts" :key="post.title">
        <div class="title">
          <h1>Title: {{ post.title }}</h1>
        </div>
        <div class="content" v-if="post.imageBase64">
          <h3>Content:</h3>
          <img :src="post.imageBase64" alt="Post image" style="max-width: 100%;" />
        </div>
        <div>
          <span v-for="tag in post.tag" :key="tag">
            <p class="tag">{{ tag }}</p>
          </span>
        </div>
      </li>
    </ul>

    <input v-model="newPostTitle" placeholder="Title" />
    <input type="file" @change="onFileChange" />
    <div>
      <h3>Select Tag:</h3>
      <div v-for="tag in tags" :key="tag">
        <input type="checkbox" :value="tag" v-model="selectedTags" />
        <label>{{ tag }}</label>
      </div>
    </div>

    <button @click="addPost()">Post</button>
    
  </div>
</template>

<style></style>