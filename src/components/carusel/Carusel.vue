<script lang="ts" setup>
import { computed, onBeforeUnmount, onMounted, ref } from 'vue';
const props = defineProps({
  images: {
    type: Array as () => { url: string }[],
    required: true,
  },
});

const currentIndex = ref(0);
const isDesktop = ref(window.innerWidth >= 768);

const selectedImages = ref<string[]>([]);

const updateIsDesktop = () => {
  isDesktop.value = window.innerWidth >= 768;
};

onMounted(() => {
  window.addEventListener('resize', updateIsDesktop);
})

onBeforeUnmount(() => {
  window.removeEventListener('resize', updateIsDesktop);
});

const prev = () => {
  if (isDesktop.value) {
    if (currentIndex.value === 0) {
      currentIndex.value = props.images.length - Math.min(5, props.images.length);
    } else {
      currentIndex.value -= 1;
    }
  } else {
    currentIndex.value = (currentIndex.value - 1 + props.images.length) % props.images.length;
  }
};

const next = () => {
  if (isDesktop.value) {
    if (currentIndex.value >= props.images.length - Math.min(5, props.images.length)) {
      currentIndex.value = 0;
    } else {
      currentIndex.value += 1;
    }
  } else {
    currentIndex.value = (currentIndex.value + 1) % props.images.length;
  }

};

const carouselStyles = computed(() => {
  const imageWidth = isDesktop.value ? 100 / Math.min(5, props.images.length) : 100;
  return {
    transform: `translateX(-${currentIndex.value * imageWidth}%)`,
  };
});

const selectImage = (index: number) => {
  const imageUrl = props.images[index].url;
  if (!selectedImages.value.includes(imageUrl)) {
    selectedImages.value.push(imageUrl);
  } else {
    selectedImages.value = selectedImages.value.filter(url => url !== imageUrl);
  }
};
</script>
<template>
  <div class="carousel-container">
    <div class="carousel" :class="{'desktop': isDesktop}">
      <div class="carousel-images" :style="carouselStyles">
        <img
          v-for="(image, index) in images"
          :key="index"
          :src="image.url"
          :alt="`Image ${index + 1}`"
          class="carousel-image"
          :class="{'selected': selectedImages.includes(image.url)}"
          @click="selectImage(index)"
        />
      </div>
      <div>
        <button @click="prev" class="carousel-button prev">Prev</button>
        <button @click="next" class="carousel-button next">Next</button>
      </div>
    </div> 
    <div class="selected-images">
      <h2>Selected Images:</h2>
      <ul>
        <li v-for="(image, index) in selectedImages" :key="index">
          <img :src="image" alt="Selected image" />
        </li>
      </ul>
    </div>
  </div>
</template>
<style>
.carousel-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 20px;
  padding: 10px;
  background-color: #f7f7f7;
  border-radius: 10px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.carousel {
  display: flex;
  align-items: center;
  position: relative;
  overflow: hidden;
  flex-direction: column;
}

.carousel-images {
  display: flex;
  transition: transform 0.3s ease-in-out;
}

.carousel-image {
  width: 100%;
  height: auto;
  cursor: pointer;
  object-fit: cover;
  opacity: 0.9;
  transition: transform 0.3s ease-in-out;
  box-sizing: border-box;
  padding: 2px;
}

.carousel-image.selected {
  border: 4px solid #ff6347;
  opacity: 1;
}

.carousel-button {
  background-color: rgba(0, 0, 0, 0.6);
  color: white;
  border: none;
  padding: 12px 18px;
  cursor: pointer;
  font-size: 18px;
  border-radius: 5px;
  transition: background-color 0.3s ease;
}

.carousel-button:hover {
  background-color: rgba(0, 0, 0, 0.8);
}

.selected-images {
  margin-top: 20px;
  text-align: center;
  
}

.selected-images ul {
  list-style-type: none;
  padding: 0;
  display: flex;
  justify-content: center;
  gap: 10px;
}

.selected-images li {
  display: inline-block;
}

.selected-images img {
  width: 50px;
  height: 50px;
  object-fit: cover;
  border-radius: 5px;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.2);
  transition: transform 0.3s ease;
}

.selected-images img:hover {
  transform: scale(1.1);
}

.desktop .carousel-image {
  width: 20%; 
}

.carousel-button.prev {
  position: absolute;
  left: 20px;
  top: 50%;
  transform: translateY(-50%);
}

.carousel-button.next {
  position: absolute;
  right: 20px;
  top: 50%;
  transform: translateY(-50%);
}

/* Mobile schema */
.mobile-carousel {
  display: flex;
  align-items: center;
  position: relative;
  overflow: hidden;
  width: 100%;
}

.mobile-carousel img {
  width: 100%; 
  height: auto;
  transition: transform 0.3s ease-in-out;
}

.mobile-carousel img:hover {
  transform: scale(1.05);
}
</style>