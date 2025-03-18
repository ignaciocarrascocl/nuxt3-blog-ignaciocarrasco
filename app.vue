<template>
  <div>
    <header>
      <h1><NuxtLink to="/">My Simple Blog</NuxtLink></h1>
    </header>
    <slot />
    <footer>
      <p>&copy; {{ new Date().getFullYear() }} My Simple Blog</p>
    </footer>
  </div>
</template>

<style lang="scss">
header, footer {
  text-align: center;
  padding: 1rem;
  background: #f8f8f8;
  border-bottom: 1px solid #ddd;
}

footer {
  margin-top: 2rem;
  border-top: 1px solid #ddd;
}
</style>