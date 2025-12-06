<template>
  <div class="showcase-ui" :class="{ active: visible }">
    <div class="scene">
      <div class="camera-rig" ref="cameraRig" :class="rigClasses">
        <div class="phone-3d" ref="phone" :class="phoneClasses">
          <div class="face front">
            <div class="screen-content">
              <div class="screen-glass"></div>
              <div class="status-bar">
                <span>{{ currentTime }}</span>
                <div>
                  <i class="fas fa-signal"></i>
                  <i class="fas fa-wifi"></i>
                  <i class="fas fa-battery-three-quarters"></i>
                </div>
              </div>
              <div class="notification-container" :style="{ top: notificationTop }">
                <div class="notification-header">
                  <div class="app-info">
                    <i class="fas fa-comment-dots app-icon-sm"></i>
                    <span>Messages</span>
                  </div>
                  <span>now</span>
                </div>
                <div class="notification-content">
                  <div class="avatar">V</div>
                  <div>
                    <div class="sender-name">Ved Sinha</div>
                    <div class="message-preview">
                      <span class="link-highlight">thook.ai</span> is coming, signup now
                    </div>
                  </div>
                </div>
              </div>
              <div class="edge-shine-overlay" ref="shine" :class="shineClasses"></div>
            </div>
          </div>
          <div class="face back"></div>
          <div class="face right"></div>
          <div class="face left"></div>
          <div class="face top"></div>
          <div class="face bottom"></div>
        </div>
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

const cameraRig = ref(null)
const phone = ref(null)
const shine = ref(null)

const currentTime = ref('12:30')
const notificationTop = ref('-120px')

const isScanning = ref(false)
const isZoomOut = ref(false)
const isSpinning = ref(false)
const isShining = ref(false)
const isPhonePositioned = ref(false)

const rigClasses = computed(() => ({
  'anim-scanning-rig': isScanning.value,
  'anim-zoom-out': isZoomOut.value
}))

const phoneClasses = computed(() => ({
  'anim-scanning-phone-pos': isPhonePositioned.value,
  'anim-spin': isSpinning.value
}))

const shineClasses = computed(() => ({
  'anim-scanning-shine': isShining.value
}))

let animationTimer = null
let clockInterval = null

const updateClock = () => {
  const now = new Date()
  const h = String(now.getHours()).padStart(2, '0')
  const m = String(now.getMinutes()).padStart(2, '0')
  currentTime.value = `${h}:${m}`
}

const runAnimation = () => {
  // Reset states
  isScanning.value = false
  isZoomOut.value = false
  isSpinning.value = false
  isShining.value = false
  isPhonePositioned.value = false
  notificationTop.value = '-120px'

  // Force reflow
  if (cameraRig.value) {
    void cameraRig.value.offsetWidth
  }

  // Start scanning animation
  isPhonePositioned.value = true
  isScanning.value = true
  isShining.value = true

  // Zoom out after scan
  setTimeout(() => {
    isScanning.value = false
    isZoomOut.value = true
  }, 3000)

  // Start spin
  setTimeout(() => {
    isPhonePositioned.value = false
    isSpinning.value = true
  }, 3500)

  // Show notification
  setTimeout(() => {
    notificationTop.value = '35px'
  }, 7600)

  // Complete animation
  animationTimer = setTimeout(() => {
    emit('animation-complete')
  }, 8400)
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
})

onUnmounted(() => {
  if (clockInterval) clearInterval(clockInterval)
  if (animationTimer) clearTimeout(animationTimer)
})
</script>

