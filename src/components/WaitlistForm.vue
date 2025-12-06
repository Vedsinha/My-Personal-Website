<template>
  <div class="newsletter-form-container">
    <form 
      v-if="!showSuccess && !showError"
      class="newsletter-form w-full flex flex-col gap-4" 
      @submit.prevent="submitHandler"
    >
      <input 
        v-model="email"
        class="dark-input w-full px-4 py-3 rounded-xl text-sm" 
        placeholder="Your Email here." 
        required 
        type="email"
        :disabled="isLoading"
      >
      
      <button 
        v-if="!isLoading"
        type="submit" 
        class="dark-btn w-full py-3 rounded-xl text-sm font-medium cursor-pointer"
      >
        Submit
      </button>
      
      <button 
        v-else
        type="button" 
        class="dark-btn w-full py-3 rounded-xl text-sm font-medium cursor-not-allowed opacity-75"
        disabled
      >
        Please wait...
      </button>
    </form>

    <div v-if="showSuccess" class="mt-4 bg-green-500/20 text-green-400 p-3 rounded-xl text-sm border border-green-500/30">
      Thanks! We'll be in touch!
    </div>
    
    <div v-if="showError" class="mt-4 bg-red-500/20 text-red-400 p-3 rounded-xl text-sm border border-red-500/30">
      {{ errorMessage }}
    </div>
    
    <button 
      v-if="showSuccess || showError"
      class="text-gray-500 text-sm mt-4 hover:text-white transition-colors block mx-auto" 
      type="button"
      @click="resetForm"
    >
      &larr; Back
    </button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const email = ref('')
const isLoading = ref(false)
const showSuccess = ref(false)
const showError = ref(false)
const errorMessage = ref('Oops! Something went wrong')

const FORM_ACTION = 'https://app.loops.so/api/newsletter-form/cmiqdo1jw0hhj1z0iffw5q01p'

const rateLimit = () => {
  showError.value = true
  errorMessage.value = 'Too many signups, please try again in a little while'
}

const submitHandler = async () => {
  const time = new Date()
  const timestamp = time.valueOf()
  const previousTimestamp = localStorage.getItem('loops-form-timestamp')
  
  if (previousTimestamp && Number(previousTimestamp) + 60000 > timestamp) {
    rateLimit()
    return
  }
  
  localStorage.setItem('loops-form-timestamp', timestamp.toString())
  isLoading.value = true
  
  const formBody = `userGroup=&mailingLists=&email=${encodeURIComponent(email.value)}`
  
  try {
    const res = await fetch(FORM_ACTION, {
      method: 'POST',
      body: formBody,
      headers: {
        'Content-Type': 'application/x-www-form-urlencoded',
      },
    })
    
    if (res.ok) {
      showSuccess.value = true
      email.value = ''
    } else {
      const data = await res.json()
      showError.value = true
      errorMessage.value = data.message || res.statusText || 'Oops! Something went wrong'
    }
  } catch (error) {
    if (error.message === 'Failed to fetch') {
      rateLimit()
      return
    }
    showError.value = true
    if (error.message) {
      errorMessage.value = error.message
    }
    localStorage.setItem('loops-form-timestamp', '')
  } finally {
    isLoading.value = false
  }
}

const resetForm = () => {
  showSuccess.value = false
  showError.value = false
  errorMessage.value = 'Oops! Something went wrong'
}
</script>

