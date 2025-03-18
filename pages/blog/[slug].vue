<template>
	<main v-if="post" class="post-container">
		<h1>{{ post.title }}</h1>
		<p class="date">{{ formatDate(post.date) }}</p>

		<!-- Featured Image -->
		<img v-if="post.featuredImage" :src="post.featuredImage" :alt="post.title" class="featured-image" />

		<!-- Blog Content (Full Markdown) -->
		<MDC :value="post.body" />

		<!-- Categories -->
		<div v-if="post.categories && post.categories.length" class="categories">
			<strong>Categories:</strong>
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
			<strong>Tags:</strong>
			<NuxtLink
				v-for="tag in post.tags"
				:key="tag"
				:to="`/blog/tag/${tag}`"
				class="tag-link"
			>
				#{{ tag }}
			</NuxtLink>
		</div>

		<!-- Author -->
		<p class="author">By: {{ post.author }}</p>

		<!-- Back to Blog -->
		<NuxtLink to="/blog" class="back-link">← Back to Blog</NuxtLink>
	</main>

	<!-- Loading State -->
	<p v-else class="loading">Loading post...</p>
</template>

<script setup>
const route = useRoute();

// Fetch blog post content
const { data: post } = reactive(await useAsyncData("post", () =>
	queryContent("/blog").where({ _path: `/blog/${route.params.slug}` }).findOne(), 
	{ default: () => ({ categories: [], tags: [], author: "Unknown" }) }
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
.categories, .tags {
	margin-top: 10px;
	font-size: 0.9rem;
	.category-link, .tag-link {
		margin-right: 10px;
		text-decoration: none;
		color: #007bff;
		font-weight: bold;
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
