<template>
  <main id="main" class="blog-post">
    <h1>{{ post?.title }}</h1>
    <p><small>Published on {{ formatDate(post?.date) }} by {{ post?.author }}</small></p>
    <img v-if="post?.featuredImage" :src="post.featuredImage" :alt="post.title" class="featured-image" />
    <div class="content">
      <MDC :value="post?.body" />
    </div>
    <NuxtLink to="/">Back to Blog</NuxtLink>
  </main>
</template>

<script setup>
import { queryContent } from '@nuxt/content';

// Get the slug from the route
const route = useRoute();
const slug = route.params.slug;

// Fetch the specific blog post
const { data: post } = await useAsyncData('blog-post', () =>
  queryContent('/blog')
    .where({ _path: `/blog/${slug}` })
    .findOne()
);

// Handle 404 if post not found
if (!post.value) {
  throw createError({ statusCode: 404, message: 'Post not found' });
}

// Format date helper
function formatDate(dateString) {
  return new Date(dateString).toLocaleDateString('en-US', {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
  });
}

// Set SEO for the post
setSeoHead(post.value?.SEOmetaData);
</script>

<style lang="scss" scoped>
main {
  display: grid;
  gap: 1rem;
  padding: 2rem;
  max-width: 50em;
  margin: 0 auto;
}

.featured-image {
  max-width: 100%;
  height: auto;
  margin: 1rem 0;
}

.content {
  line-height: 1.6;
}
</style>