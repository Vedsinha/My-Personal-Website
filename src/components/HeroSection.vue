<template>
  <div 
    class="hero-section" 
    :style="{ backgroundColor: heroBackground }"
  >
    <!-- PHASE 1: SEARCH UI -->
    <SearchUI 
      :text="currentSearchText"
      :visible="searchVisible"
      :hidden="searchHidden"
      :start-typing="shouldStartTyping"
      :is-active="searchActive"
      @typing-complete="onTypingComplete"
    />

    <!-- PHASE 2: SAMSUNG SHOWCASE -->
    <SamsungShowcase 
      :visible="samsungVisible"
      :start-animation="samsungAnimating"
      @animation-complete="onSamsungComplete"
    />

    <!-- PHASE 3: IPHONE SHOWCASE -->
    <IPhoneShowcase 
      :visible="iphoneVisible"
      :start-animation="iphoneAnimating"
      @animation-complete="onIPhoneComplete"
    />

    <!-- PHASE 4: AQI MAP -->
    <AQIMap 
      :visible="aqiVisible"
      :start-animation="aqiAnimating"
      @animation-complete="onAQIComplete"
    />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import SearchUI from './SearchUI.vue'
import SamsungShowcase from './SamsungShowcase.vue'
import IPhoneShowcase from './IPhoneShowcase.vue'
import AQIMap from './AQIMap.vue'

// Hero background color
const heroBackground = ref('#ffffff')

// Search UI state
const searchVisible = ref(true)
const searchHidden = ref(false)
const searchActive = ref(false)
const currentSearchText = ref('Create iPhone 17 Notifications')
const shouldStartTyping = ref(false)

// Showcase visibility states
const iphoneVisible = ref(false)
const iphoneAnimating = ref(false)

const samsungVisible = ref(false)
const samsungAnimating = ref(false)

const aqiVisible = ref(false)
const aqiAnimating = ref(false)

// Current phase tracking
const currentPhase = ref('search-iphone') // search-iphone, iphone, search-samsung, samsung, search-aqi, aqi

// Sleep utility
const sleep = (ms) => new Promise(r => setTimeout(r, ms))

// Phase 1: Search -> iPhone
const onTypingComplete = async () => {
  if (currentPhase.value === 'search-iphone') {
    searchActive.value = true
    await sleep(150)
    searchVisible.value = false
    heroBackground.value = '#0f0f11'
    await sleep(500)
    searchHidden.value = true
    currentPhase.value = 'iphone'
    iphoneVisible.value = true
    iphoneAnimating.value = true
  } else if (currentPhase.value === 'search-samsung') {
    searchActive.value = true
    await sleep(150)
    searchVisible.value = false
    heroBackground.value = '#000000'
    await sleep(800)
    searchHidden.value = true
    currentPhase.value = 'samsung'
    samsungVisible.value = true
    samsungAnimating.value = true
  } else if (currentPhase.value === 'search-aqi') {
    searchActive.value = true
    await sleep(150)
    searchVisible.value = false
    heroBackground.value = '#0f172a'
    await sleep(800)
    searchHidden.value = true
    currentPhase.value = 'aqi'
    aqiVisible.value = true
    aqiAnimating.value = true
  }
}

// Phase 2: iPhone complete -> Reset to Search for Samsung
const onIPhoneComplete = async () => {
  iphoneVisible.value = false
  iphoneAnimating.value = false
  
  // Reset to search
  heroBackground.value = '#ffffff'
  searchActive.value = false
  currentSearchText.value = 'Create Android Phone showcase'
  searchHidden.value = false
  
  await sleep(100)
  searchVisible.value = true
  currentPhase.value = 'search-samsung'
  
  await sleep(1000)
  shouldStartTyping.value = false
  await sleep(50)
  shouldStartTyping.value = true
}

// Phase 3: Samsung complete -> Reset to Search for AQI
const onSamsungComplete = async () => {
  samsungVisible.value = false
  samsungAnimating.value = false
  
  // Reset to search
  heroBackground.value = '#ffffff'
  searchActive.value = false
  currentSearchText.value = 'Create India AQI map'
  searchHidden.value = false
  
  await sleep(100)
  searchVisible.value = true
  currentPhase.value = 'search-aqi'
  
  await sleep(1000)
  shouldStartTyping.value = false
  await sleep(50)
  shouldStartTyping.value = true
}

// Phase 4: AQI complete -> Scroll to product section
const onAQIComplete = () => {
  const productSection = document.getElementById('product-section')
  if (productSection) {
    productSection.scrollIntoView({ behavior: 'smooth' })
  }
}

// Start the animation sequence
onMounted(() => {
  setTimeout(() => {
    shouldStartTyping.value = true
  }, 300)
})
</script>

