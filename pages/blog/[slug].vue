<template>
	<main v-if="post" class="post-container">
		<h1>{{ post.title }}</h1>
		<p class="date">{{ formatDate(post.date) }}</p>

		<!-- Imagen destacada -->
		<img v-if="post.featuredImage" :src="post.featuredImage" :alt="post.title" class="featured-image" />

		<!-- Contenido del post (Markdown procesado) -->
		<MDC :value="post.body" />

		<!-- Categorías -->
		<div v-if="post.categories && post.categories.length" class="categories">
			<strong>Categorías:</strong>
			<NuxtLink
				v-for="category in post.categories"
				:key="category"
				:to="`/blog/category/${category}`"
				class="category-link"
			>
				{{ category }}
			</NuxtLink>
		</div>

		<!-- Tags -->
		<div v-if="post.tags && post.tags.length" class="tags">
			<strong>Etiquetas:</strong>
			<NuxtLink
				v-for="tag in post.tags"
				:key="tag"
				:to="`/blog/tag/${tag}`"
				class="tag-link"
			>
				{{ tag }}
			</NuxtLink>
		</div>
	</main>
</template>

<script setup>
import { useRoute } from 'vue-router';
import { useAsyncData } from '#app';

const route = useRoute();
const { data: post } = await useAsyncData("post", () =>
  queryContent("/blog")
    .where({ slug: route.params.slug })
    .findOne()
);

const formatDate = (dateString) => {
  const date = new Date(dateString);
  return `${date.getDate()}/${date.getMonth() + 1}/${date.getFullYear()}`;
};
</script>

<style scoped>
.post-container {
  max-width: 800px;
  margin: 0 auto;
}
.featured-image {
  width: 100%;
  margin-bottom: 1rem;
}
.categories, .tags {
  margin-top: 2rem;
}
.category-link, .tag-link {
  margin-right: 10px;
  color: #007BFF;
}
</style>
