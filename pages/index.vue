<template>
  <main id="main" class="home">
    <section class="blog-list">
      <h1>Blog Posts</h1>
      <div v-for="post in posts" :key="post._path" class="blog-post">
        <h2>
          <NuxtLink :to="post._path">{{ post.title }}</NuxtLink>
        </h2>
        <p>{{ post.description }}</p>
        <time>{{ formatDate(post.date) }}</time>
      </div>
      <nav class="pagination">
        <button
          :disabled="page === 1"
          @click="page--"
        >
          Previous
        </button>
        <span>Page {{ page }} of {{ totalPages }}</span>
        <button
          :disabled="page === totalPages"
          @click="page++"
        >
          Next
        </button>
      </nav>
    </section>
  </main>
</template>

<script setup>
import { reactive, computed } from 'vue';

// Fetch blog posts with pagination
const page = reactive({ value: 1 });
const perPage = 5; // Number of posts per page

const { data: postsData } = await useAsyncData('blog-posts', () =>
  queryContent('/blog')
    .sort({ date: -1 }) // Sort by date, newest first
    .limit(perPage)
    .skip((page.value - 1) * perPage)
    .find()
);

const posts = computed(() => postsData.value || []);
const total = await queryContent('/blog').count();
const totalPages = Math.ceil(total / perPage);

// Watch page changes to refetch data
watch(() => page.value, async () => {
  const { data: newPosts } = await useAsyncData('blog-posts', () =>
    queryContent('/blog')
      .sort({ date: -1 })
      .limit(perPage)
      .skip((page.value - 1) * perPage)
      .find()
  );
  posts.value = newPosts.value || [];
});

// Format date function
const formatDate = (date) => {
  return new Date(date).toLocaleDateString('en-US', {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
  });
};

// SEO setup (optional, adjust as needed)
const seoMeta = {
  title: 'Blog - My Site',
  description: 'Latest blog posts',
  // Add other SEO metadata as needed
};
setSeoHead(seoMeta);

</script>

<style lang="scss" scoped>
main {
  display: grid;
  justify-items: center;
  align-items: start;
  padding: 2rem;
}

.blog-list {
  max-width: 50em;
  width: 100%;
}

.blog-post {
  margin-bottom: 2rem;
  padding: 1rem;
  border: 1px solid #ddd;
  border-radius: 4px;
}

.blog-post h2 {
  margin: 0 0 0.5rem;
  font-size: 1.5rem;
}

.blog-post p {
  margin: 0 0 0.5rem;
  color: #666;
}

.blog-post time {
  font-size: 0.9rem;
  color: #999;
}

.pagination {
  display: flex;
  gap: 1rem;
  justify-content: center;
  margin-top: 2rem;
}

.pagination button {
  padding: 0.5rem 1rem;
  cursor: pointer;
}

.pagination button:disabled {
  cursor: not-allowed;
  opacity: 0.5;
}

@include fade-in;
</style>