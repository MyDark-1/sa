<template>
  <div class="home-page">
    <NetworkCalculator />
  </div>
</template>

<script setup lang="ts">
import NetworkCalculator from '../components/NetworkCalculator.vue';
</script>

<style scoped>
.home-page {
  min-height: 100vh;
  padding: 20px;
  background-color: var(--color-background);
}
</style>
