<template>
	<main class="blog-list">
		<h1>Posts with Tag: #{{ route.params.tag }}</h1>
		
		<div v-if="posts.length">
			<article v-for="post in posts" :key="post._path" class="blog-post">
				<h2>
					<NuxtLink :to="`/blog/${post._path.split('/').pop()}`">
						{{ post.title }}
					</NuxtLink>
				</h2>
				<p>{{ post.summary || post.body.slice(0, 140) + "..." }}</p>
				<NuxtLink :to="`/blog/${post._path.split('/').pop()}`">Read More</NuxtLink>
			</article>
		</div>

		<p v-else>No posts found with this tag.</p>

		<NuxtLink to="/blog">← Back to Blog</NuxtLink>
	</main>
</template>

<script setup>
const route = useRoute();

// Fetch posts matching the tag
const { data: posts } = reactive(await useAsyncData("tagPosts", () =>
	queryContent("/blog").where({ tags: { $contains: route.params.tag } }).find()
));
</script>

<style scoped>
.blog-list {
	max-width: 800px;
	margin: auto;
	padding: 20px;
}
.blog-post {
	margin-bottom: 20px;
}
</style>
