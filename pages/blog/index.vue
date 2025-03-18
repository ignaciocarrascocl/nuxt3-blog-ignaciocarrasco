<template>
	<main id="main" class="home">
		<section v-if="paginatedPosts.length">
			<article v-for="post in paginatedPosts" :key="post._path" class="post-card">
				<NuxtLink :to="post._path">
					<h2>{{ post.title }}</h2>
					<p>{{ post.summary || getPreviewText(post.body) }}</p>
					<small>{{ formatDate(post.date) }}</small>
				</NuxtLink>
			</article>
		</section>

		<!-- Pagination Controls -->
		<div v-if="totalPages > 1" class="pagination">
			<button :disabled="currentPage === 1" @click="prevPage">← Prev</button>
			<span>Page {{ currentPage }} of {{ totalPages }}</span>
			<button :disabled="currentPage === totalPages" @click="nextPage">Next →</button>
		</div>

		<p v-else>No blog posts found.</p>
	</main>
</template>

<script setup>
const POSTS_PER_PAGE = 10;

// Fetch blog posts
const { data: posts } = reactive(await useAsyncData("posts", () =>
	queryContent("/blog").sort({ date: -1 }).find()
));

// Pagination state
const currentPage = useState("currentPage", () => 1);
const totalPages = computed(() => Math.ceil(posts.length / POSTS_PER_PAGE));

// Paginated posts
const paginatedPosts = computed(() => {
	const start = (currentPage.value - 1) * POSTS_PER_PAGE;
	return posts.slice(start, start + POSTS_PER_PAGE);
});

// Functions for pagination
const nextPage = () => { if (currentPage.value < totalPages.value) currentPage.value++; };
const prevPage = () => { if (currentPage.value > 1) currentPage.value--; };

// Function to extract text preview (140 chars)
const getPreviewText = (body) => {
	if (!body) return "No content available.";
	const text = typeof body === "string" ? body : body.toString();
	return text.slice(0, 140) + "...";
};

// Function to format dates
const formatDate = (date) => new Date(date).toLocaleDateString();
</script>

<style lang="scss" scoped>
.home {
	display: flex;
	flex-direction: column;
	align-items: center;
	gap: 1rem;
}
.post-card {
	padding: 1rem;
	border: 1px solid #ddd;
	border-radius: 8px;
	max-width: 600px;
	width: 100%;
}
.pagination {
	display: flex;
	gap: 1rem;
	margin-top: 1rem;
	button {
		padding: 0.5rem 1rem;
		cursor: pointer;
		border: none;
		background: #007bff;
		color: white;
		border-radius: 4px;
		&:disabled {
			opacity: 0.5;
			cursor: not-allowed;
		}
	}
}
</style>
