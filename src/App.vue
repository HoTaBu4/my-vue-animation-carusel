<script setup lang="ts">
import { ref, onMounted } from 'vue';
import Carusel from './components/carusel/Carusel.vue';

interface Image {
  url: string;
}

const images = ref<Image[]>([]);
const loading = ref(true); 
const error = ref<string | null>(null); 

onMounted(() => {
  const fetchImages = async () => {
    try {
      const response = await fetch('https://picsum.photos/v2/list?page=1&limit=15');
      
      if (!response.ok) {
        throw new Error('Failed to fetch images');
      }

      const data = await response.json();
      images.value = data.map((item: { download_url: string }) => ({
        url: item.download_url,
        fallbackUrl: 'path/to/fallback-image.jpg',
      }));

    } catch (err) {
      error.value = err instanceof Error ? err.message : 'An unexpected error occurred';
    } finally {
      loading.value = false;
    }
  };

  fetchImages();
});
</script>

<template>
  <header class="header">
    <h1>Carusel</h1>
  </header>
  <main class="wrapper">
    <div v-if="loading" class="loader">
      <span>Loading...</span>
    </div>

    <div v-if="error" class="error">
      <span>Error: {{ error }}</span>
    </div>
    <Carusel v-if="!loading && !error" :images="images" />
  </main>
</template>

<style scoped>
.header {
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 20px;
  padding: 10px;
  border-radius: 10px;
}
.wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
  position: relative;
}

.loader {
  position: absolute;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 24px;
  color: #333;
}

.error {
  position: absolute;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 20px;
  color: red;
}
</style>
