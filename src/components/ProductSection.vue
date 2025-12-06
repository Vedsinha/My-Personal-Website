<template>
  <section id="product-section" class="product-section">
    <div class="max-w-5xl mx-auto text-center w-full relative z-10">
      
      <!-- Headline -->
      <h1 class="text-5xl md:text-7xl font-extrabold text-white mb-6 tracking-tight leading-tight">
        10x Your Video <span class="text-transparent bg-clip-text bg-gradient-to-r from-orange-400 to-amber-600">Production Value</span>.<br>
        0x The Effort.
      </h1>

      <!-- Subheadline -->
      <p class="text-xl text-gray-400 mb-16 max-w-2xl mx-auto leading-relaxed">
        The first AI tool that turns your script into high-retention motion graphics. 
        <br class="hidden md:block">
        Stop outsourcing. Stop struggling with keyframes. 
        <span class="font-semibold text-white">Just type and drag-and-drop.</span>
      </p>

      <!-- Feature Grid -->
      <div class="grid md:grid-cols-2 gap-6 mb-24 text-left">
        <FeatureCard 
          icon="fa-solid fa-font"
          icon-color-class="text-orange-400"
          title="Kinetic Typography"
          description="Make your points pop with auto-generated animated text that syncs perfectly."
        />
        
        <FeatureCard 
          icon="fa-solid fa-layer-group"
          icon-color-class="text-purple-400"
          title="UI Mockups"
          description="Generate iPhone notifications, tweets, chat bubbles—anything & everything instantly."
        />

        <FeatureCard 
          icon="fa-solid fa-map"
          icon-color-class="text-green-400"
          title="Maps & Charts"
          description="Visualize complex data without the headache. Just paste your CSV."
        />

        <FeatureCard 
          icon="fa-solid fa-film"
          icon-color-class="text-blue-400"
          title="Pro Export"
          description="Export with transparent backgrounds. Drag straight into Premiere, DaVinci, or Final Cut."
        />
      </div>

      <!-- Waitlist Form Container -->
      <div class="max-w-md mx-auto relative group">
        <!-- Background Blur Glow -->
        <div class="absolute -inset-1 bg-gradient-to-r from-orange-600 to-amber-600 rounded-[26px] blur opacity-25 group-hover:opacity-50 transition duration-1000 group-hover:duration-200"></div>
        
        <div class="glossy-card p-10 relative">
          <!-- Badge -->
          <div class="flex justify-center mb-6">
            <span class="px-4 py-1.5 rounded-full border border-orange-500/30 bg-orange-500/10 text-orange-400 text-xs font-medium tracking-wide uppercase">
              coming soon in 2025...
            </span>
          </div>

          <!-- Card Headline -->
          <h3 class="text-3xl font-light text-white mb-2 tracking-tight">Join the waitlist</h3>
          <p class="text-sm text-gray-400 mb-8 font-light">
            Join the waitlist for early access and exclusive updates. <br>
            Be the first to experience what's next.
          </p>

          <!-- Form -->
          <WaitlistForm />

          <!-- Socials -->
          <div class="flex justify-center gap-6 mt-8 pt-6 border-t border-white/5">
            <a href="#" class="social-link"><i class="fa-brands fa-x-twitter text-lg"></i></a>
            <a href="#" class="social-link"><i class="fa-brands fa-linkedin text-lg"></i></a>
            <a href="#" class="social-link"><i class="fa-brands fa-instagram text-lg"></i></a>
            <a href="#" class="social-link"><i class="fa-brands fa-youtube text-lg"></i></a>
          </div>
        </div>
      </div>

      <!-- Footer -->
      <div class="mt-20 text-gray-600 text-xs">
        &copy; 2025 thook.ai. All rights reserved.
      </div>
    </div>
  </section>
</template>

<script setup>
import FeatureCard from './FeatureCard.vue'
import WaitlistForm from './WaitlistForm.vue'
</script>

