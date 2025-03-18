<template>
  <main id="main" class="blog-list">
    <h1>Blog Posts</h1>
    <div class="posts">
      <article v-for="post in paginatedPosts" :key="post._path" class="post">
        <h2>
          <NuxtLink :to="post._path">{{ post.title }}</NuxtLink>
        </h2>
        <p v-if="post.summary">{{ post.summary }}</p>
        <p><small>Published on {{ formatDate(post.date) }} by {{ post.author }}</small></p>
      </article>
    </div>

    <!-- Pagination Controls -->
    <div class="pagination">
      <button @click="currentPage--" :disabled="currentPage === 1">Previous</button>
      <span>Page {{ currentPage }} of {{ totalPages }}</span>
      <button @click="currentPage++" :disabled="currentPage === totalPages">Next</button>
    </div>
  </main>
</template>

<script setup>
import { queryContent } from '@nuxt/content';

// Fetch all blog posts, sorted by date descending
const { data: posts } = await useAsyncData('blog-posts', () =>
  queryContent('/blog')
    .where({ draft: false }) // Exclude drafts
    .sort({ date: -1 }) // Newest first
    .find()
);

// Pagination logic
const postsPerPage = 5; // Number of posts per page
const currentPage = ref(1);

// Compute total pages
const totalPosts = computed(() => posts.value?.length || 0);
const totalPages = computed(() => Math.ceil(totalPosts.value / postsPerPage));

// Compute paginated posts
const paginatedPosts = computed(() => {
  const start = (currentPage.value - 1) * postsPerPage;
  const end = start + postsPerPage;
  return posts.value?.slice(start, end) || [];
});

// Format date helper
function formatDate(dateString) {
  return new Date(dateString).toLocaleDateString('en-US', {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
  });
}

// Set SEO for the homepage
setSeoHead({
  metaTitle: 'My Simple Blog',
  metaDescription: 'A collection of blog posts on various topics.',
});
</script>

<style lang="scss" scoped>
main {
  display: grid;
  gap: 2rem;
  padding: 2rem;
  max-width: 50em;
  margin: 0 auto;
}

.posts {
  display: grid;
  gap: 1.5rem;
}

.post {
  padding: 1rem;
  border-bottom: 1px solid #ddd;
}

.post h2 {
  margin: 0 0 0.5rem;
}

.post p {
  margin: 0.5rem 0;
}

.pagination {
  display: flex;
  gap: 1rem;
  justify-content: center;
  align-items: center;
  margin-top: 2rem;
}

button {
  padding: 0.5rem 1rem;
  cursor: pointer;
  background: #f0f0f0;
  border: 1px solid #ccc;
  border-radius: 4px;
}

button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
</style>