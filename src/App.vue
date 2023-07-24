<script setup lang="ts">
import BGRemoval from './components/BGRemoval.vue';
</script>

<template>
  <BGRemoval />
  <footer>
    <span>All rights reserved</span>
    <span>&copy;</span>
    <span>Syahrul Faiz Ramdhani</span>
    <span> 2023</span>
  </footer>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
