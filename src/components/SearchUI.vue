<template>
  <div class="search-ui relative w-full max-w-xl px-4" :style="{ opacity: visible ? 1 : 0, transform: visible ? 'translateY(0)' : 'translateY(-20px)', display: hidden ? 'none' : 'block' }">
    <div class="search-container bg-white rounded-full flex items-center p-2 w-full border border-gray-200">
      <div class="pl-4 pr-3 text-gray-400">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
          <path stroke-linecap="round" stroke-linejoin="round" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
        </svg>
      </div>
      <div class="flex-grow h-12 flex items-center overflow-hidden relative">
        <span class="text-xl text-gray-800 font-medium whitespace-nowrap">{{ displayText }}</span>
        <span class="cursor" :class="{ typing: isTyping }"></span>
      </div>
      <button 
        class="search-btn p-3 rounded-full shadow-md flex-shrink-0 text-white transition-colors"
        :class="isActive ? 'bg-green-500 scale-95' : 'bg-blue-500 hover:bg-blue-600'"
      >
        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
          <path stroke-linecap="round" stroke-linejoin="round" d="M14 5l7 7m0 0l-7 7m7-7H3" />
        </svg>
      </button>
    </div>
    <div class="absolute bottom-[-100px] w-full text-center text-gray-400 text-sm scroll-hint">
      Scroll to explore <i class="fas fa-chevron-down ml-1"></i>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'

const props = defineProps({
  text: {
    type: String,
    default: ''
  },
  visible: {
    type: Boolean,
    default: true
  },
  hidden: {
    type: Boolean,
    default: false
  },
  startTyping: {
    type: Boolean,
    default: false
  },
  isActive: {
    type: Boolean,
    default: false
  }
})

const emit = defineEmits(['typing-complete'])

const displayText = ref('')
const isTyping = ref(false)
let charIndex = 0

const getRandomSpeed = () => Math.floor(Math.random() * 15) + 10

const typeCharacter = () => {
  if (charIndex < props.text.length) {
    isTyping.value = true
    displayText.value += props.text.charAt(charIndex)
    charIndex++
    setTimeout(typeCharacter, getRandomSpeed())
  } else {
    isTyping.value = false
    emit('typing-complete')
  }
}

const resetAndType = () => {
  displayText.value = ''
  charIndex = 0
  setTimeout(typeCharacter, 300)
}

watch(() => props.startTyping, (newVal) => {
  if (newVal) {
    resetAndType()
  }
})

watch(() => props.text, () => {
  displayText.value = ''
  charIndex = 0
})

onMounted(() => {
  if (props.startTyping && props.text) {
    resetAndType()
  }
})
</script>

