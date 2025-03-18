<template>
	<main id="main" class="home">
		<section v-if="posts.length">
			<article v-for="post in posts" :key="post._path" class="post-card">
				<NuxtLink :to="post._path">
					<h2>{{ post.title }}</h2>
					<p>{{ post.summary || post.body.substring(0, 150) + "..." }}</p>
					<small>{{ formatDate(post.date) }}</small>
				</NuxtLink>
			</article>
		</section>
		<p v-else>No blog posts found.</p>
	</main>
</template>

<script setup>
const { data: posts } = reactive(await useAsyncData("posts", () =>
	queryContent("/blog").sort({ date: -1 }).find()
));

// Optional: Format Date
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
</style>
