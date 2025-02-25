<script setup lang="ts">
import { ref, onMounted } from 'vue';
import Carusel from './components/carusel/Carusel.vue';

interface Image {
  url: string;
}

const images = ref<Image[]>([]);

onMounted(() => {

  const fetchImages = async () => {
    const response = await fetch('https://picsum.photos/v2/list?page=1&limit=10');
    const data = await response.json();
    images.value = data.map((item: { download_url: string }) => ({
      url: item.download_url,
      fallbackUrl: 'path/to/fallback-image.jpg',
    }));
  };

  fetchImages();
});

</script>


<template>
  <header class="header">
    <h1>Carusel</h1>
  </header>
  <main class="wrapper">
    <Carusel :images="images" />
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
}
</style>
