<template>
  <div class="feature-card p-6 rounded-2xl">
    <div 
      class="w-10 h-10 bg-white/10 rounded-lg flex items-center justify-center mb-4 text-xl"
      :class="iconColorClass"
    >
      <i :class="icon"></i>
    </div>
    <h3 class="font-bold text-lg text-white mb-2">{{ title }}</h3>
    <p class="text-gray-400 text-sm">{{ description }}</p>
  </div>
</template>

<script setup>
defineProps({
  icon: {
    type: String,
    required: true
  },
  iconColorClass: {
    type: String,
    default: 'text-orange-400'
  },
  title: {
    type: String,
    required: true
  },
  description: {
    type: String,
    required: true
  }
})
</script>

