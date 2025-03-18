<template>
	<main v-if="post" class="post-container">
		<h1>{{ post.title }}</h1>
		<p class="date">{{ formatDate(post.date) }}</p>
		<img v-if="post.featuredImage" :src="post.featuredImage" :alt="post.title" class="featured-image" />
		<MDC :value="post.body" />
		<p class="author">By: {{ post.author }}</p>

		<!-- Back to blog link -->
		<NuxtLink to="/" class="back-link">← Back to Blog</NuxtLink>
	</main>

	<p v-else class="loading">Loading post...</p>
</template>

<script setup>
const route = useRoute();

// Fetch the post based on the slug from the URL
const { data: post } = reactive(await useAsyncData("post", () =>
	queryContent("/blog", route.params.slug).findOne())
);

// Function to format dates
const formatDate = (date) => new Date(date).toLocaleDateString();
</script>

<style lang="scss" scoped>
.post-container {
	max-width: 800px;
	margin: auto;
	padding: 20px;
}
.featured-image {
	width: 100%;
	max-height: 400px;
	object-fit: cover;
	border-radius: 8px;
}
.date {
	color: #888;
	font-size: 0.9rem;
	margin-bottom: 10px;
}
.author {
	margin-top: 20px;
	font-weight: bold;
}
.back-link {
	display: inline-block;
	margin-top: 20px;
	color: #007bff;
	text-decoration: none;
}
</style>
