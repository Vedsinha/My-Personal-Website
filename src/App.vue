<template>
  <div id="app-root">
    <HeroSection />
    <ProductSection />
  </div>
</template>

<script setup>
import HeroSection from './components/HeroSection.vue'
import ProductSection from './components/ProductSection.vue'
</script>

<style>
#app-root {
  min-height: 100vh;
}
</style>
