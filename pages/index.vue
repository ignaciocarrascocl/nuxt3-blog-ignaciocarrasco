<template>
	<main id="main" class="home">
		<div v-for="post in posts" :key="post.slug" class="post-summary">
			<h2>{{ post.title }}</h2>
			<p>{{ post.summary || post.body.substring(0, 140) + '...' }}</p>
			<NuxtLink :to="`/blog/${post.slug}`" class="read-more">Leer más</NuxtLink>
			<p class="date">{{ formatDate(post.date) }}</p>
		</div>
		<!-- Paginación -->
		<Pagination :currentPage="currentPage" :totalPages="totalPages" @changePage="loadPosts" />
	</main>
</template>

<script setup>
import { ref, reactive } from 'vue';
import { useAsyncData } from '#app';
import Pagination from '~/components/Pagination.vue';  // Asegúrate de tener un componente de paginación

const currentPage = ref(1);
const totalPages = ref(0);
const posts = ref([]);

const loadPosts = async (page = 1) => {
  currentPage.value = page;
  const { data } = await useAsyncData("posts", () =>
    queryContent("/blog")
      .sort("-date")
      .limit(10)
      .skip((page - 1) * 10)  // Paginación
      .find()
  );
  posts.value = data;
  totalPages.value = Math.ceil(data.length / 10); // Ajusta la lógica para obtener el total de páginas
};

const formatDate = (dateString) => {
  const date = new Date(dateString);
  return `${date.getDate()}/${date.getMonth() + 1}/${date.getFullYear()}`;
};

// Cargar los posts al iniciar
loadPosts(currentPage.value);
</script>

<style scoped>
.post-summary {
  margin-bottom: 2rem;
}
.read-more {
  display: inline-block;
  margin-top: 1rem;
  color: #007BFF;
}
.date {
  font-size: 0.9rem;
  color: gray;
}
</style>
