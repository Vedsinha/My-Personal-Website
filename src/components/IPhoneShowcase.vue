<template>
  <div class="iphone-ui" :class="{ active: visible }">
    <div 
      class="iphone-chassis" 
      ref="iphoneRef"
      :style="{ transform: phoneTransform }"
    >
      <div class="btn-side btn-mute"></div>
      <div class="btn-side btn-vol-up"></div>
      <div class="btn-side btn-vol-down"></div>
      <div class="btn-side btn-power"></div>
      <div class="iphone-screen" :class="{ active: screenActive }">
        <div class="reflection"></div>
        <div class="screen-dimmer"></div>
        <div class="date-widget">{{ currentDate }}</div>
        <div class="time-widget">{{ currentTime }}</div>
        <div class="dynamic-island" :class="{ expanded: islandExpanded }">
          <div class="camera-cutout"></div>
          <div class="island-content w-full h-full flex flex-col justify-between">
            <div class="flex items-center gap-3 mb-2">
              <div class="app-icon">
                <i class="fa-solid fa-bolt"></i>
              </div>
              <div class="flex flex-col">
                <div class="flex items-center gap-1">
                  <span class="text-white font-bold text-sm tracking-wide">THOOK.AI</span>
                  <span class="text-gray-400 text-xs">now</span>
                </div>
                <span class="text-white text-xs font-medium">Platform Launch</span>
              </div>
            </div>
            <div class="text-gray-200 text-sm leading-tight mb-3 pl-1">
              thook.ai is coming. Join the waitlist for beta access and max discount.
            </div>
            <div class="flex gap-2 mt-auto">
              <button class="cta-btn w-full" @click="pulseSuccess">
                <span v-if="!joined">Join Waitlist</span>
                <span v-else><i class="fa-solid fa-check"></i> Joined!</span>
              </button>
            </div>
          </div>
        </div>
        <div class="lock-btn lock-btn-left">
          <svg viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
            <path d="M6 2h12v6l-2 3v9a2 2 0 0 1-2 2H10a2 2 0 0 1-2-2v-9l-2-3V2zm2 2v3h8V4H8zm1 16h6v-8H9v8z"/>
          </svg>
        </div>
        <div class="lock-btn lock-btn-right">
          <i class="fa-solid fa-camera"></i>
        </div>
        <div class="home-bar"></div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch, onUnmounted } from 'vue'

const props = defineProps({
  visible: {
    type: Boolean,
    default: false
  },
  startAnimation: {
    type: Boolean,
    default: false
  }
})

const emit = defineEmits(['animation-complete'])

const iphoneRef = ref(null)
const screenActive = ref(false)
const islandExpanded = ref(false)
const joined = ref(false)
const mouseX = ref(0)
const mouseY = ref(0)

const currentTime = ref('9:41')
const currentDate = ref('Monday, 9 June')

let animationTimer = null
let clockInterval = null

const phoneTransform = computed(() => {
  if (!props.visible) return ''
  return `rotateY(${mouseX.value}deg) rotateX(${mouseY.value}deg)`
})

const updateClock = () => {
  const now = new Date()
  const h12 = now.getHours() % 12 || 12
  const m = String(now.getMinutes()).padStart(2, '0')
  currentTime.value = `${h12}:${m}`
  
  const options = { weekday: 'long', month: 'long', day: 'numeric' }
  currentDate.value = now.toLocaleDateString('en-US', options)
}

const handleMouseMove = (e) => {
  if (!props.visible) return
  mouseX.value = (window.innerWidth / 2 - e.pageX) / 30
  mouseY.value = (window.innerHeight / 2 - e.pageY) / 30
}

const runAnimation = () => {
  // Reset states
  screenActive.value = false
  islandExpanded.value = false
  joined.value = false
  
  // Initial tilt
  mouseX.value = -5
  mouseY.value = 5
  
  // Straighten phone
  setTimeout(() => {
    mouseX.value = 0
    mouseY.value = 0
    screenActive.value = true
  }, 1000)
  
  // Expand Dynamic Island
  setTimeout(() => {
    islandExpanded.value = true
  }, 1800)
  
  // Complete animation
  animationTimer = setTimeout(() => {
    emit('animation-complete')
  }, 2600)
}

const pulseSuccess = () => {
  joined.value = true
  setTimeout(() => {
    islandExpanded.value = false
    setTimeout(() => {
      joined.value = false
    }, 500)
  }, 1500)
}

watch(() => props.startAnimation, (newVal) => {
  if (newVal && props.visible) {
    runAnimation()
  }
})

watch(() => props.visible, (newVal) => {
  if (newVal && props.startAnimation) {
    runAnimation()
  }
})

onMounted(() => {
  updateClock()
  clockInterval = setInterval(updateClock, 1000)
  document.addEventListener('mousemove', handleMouseMove)
})

onUnmounted(() => {
  if (clockInterval) clearInterval(clockInterval)
  if (animationTimer) clearTimeout(animationTimer)
  document.removeEventListener('mousemove', handleMouseMove)
})
</script>

<style scoped>
.cta-btn.joined {
  background: #4ade80;
  color: #000;
  animation: none;
}
</style>

