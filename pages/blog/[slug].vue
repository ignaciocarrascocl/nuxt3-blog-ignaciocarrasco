<template>
	<main v-if="post" class="post-container">
		<h1>{{ post.title }}</h1>
		<p class="date">{{ formatDate(post.date) }}</p>

		<!-- Featured Image -->
		<img v-if="post.featuredImage" :src="post.featuredImage" :alt="post.title" class="featured-image" />

		<!-- Blog Content -->
		<MDC :value="post.body" />

		<!-- Tags Section -->
		<div v-if="post.tags && post.tags.length" class="tags">
			<span v-for="tag in post.tags" :key="tag" class="tag">
				#{{ tag }}
			</span>
		</div>

		<!-- Author -->
		<p class="author">By: {{ post.author }}</p>

		<!-- Back to Blog -->
		<NuxtLink to="/" class="back-link">← Back to Blog</NuxtLink>
	</main>

	<!-- Loading State -->
	<p v-else class="loading">Loading post...</p>
</template>

<script setup>
const route = useRoute();

// Fetch the post and set default values to prevent errors
const { data: post } = reactive(await useAsyncData("post", () =>
	queryContent("/blog", route.params.slug).findOne(), { default: () => ({ tags: [], author: "Unknown" }) }
));

// Function to format dates
const formatDate = (date) => (date ? new Date(date).toLocaleDateString() : "No date available");
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
.tags {
	margin-top: 10px;
	.tag {
		display: inline-block;
		background: #f3f3f3;
		color: #333;
		padding: 5px 10px;
		border-radius: 5px;
		margin-right: 5px;
		font-size: 0.9rem;
	}
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
