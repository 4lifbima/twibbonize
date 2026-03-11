<template>
  <footer class="border-t border-slate-200 dark:border-white/5 px-6 pt-16 pb-8 mt-auto bg-slate-50 dark:bg-app-bg transition-colors duration-300">
    <div class="max-w-6xl mx-auto flex flex-wrap justify-between items-start gap-12">
      <!-- Brand -->
      <div class="max-w-xs">
        <div class="flex items-center gap-2.5 flex-shrink-0 mb-4 group inline-flex">
          <div class="w-8 h-8 rounded-lg bg-gradient-to-br from-violet-600 to-pink-500 flex items-center justify-center text-white shadow-md group-hover:scale-105 transition-transform">
            <Icon icon="ph:magic-wand-fill" class="w-5 h-5" />
          </div>
          <span class="font-sans text-xl font-extrabold text-slate-900 dark:text-white tracking-tight">
            Twibbon<span class="text-violet-600 dark:text-gold">Craft</span>
          </span>
        </div>
        <p class="text-sm font-medium text-slate-500 dark:text-white/40 leading-relaxed mb-6">
          Create, customize, and share stunning twibbon frames that perfectly express who you are.
        </p>
      </div>
      
    </div>
    
    <div class="max-w-6xl mx-auto mt-12 pt-6 border-t border-slate-200 dark:border-white/[0.04] flex flex-col sm:flex-row items-center justify-between gap-4">
      <div class="text-xs font-bold text-slate-400 dark:text-white/30">
        &copy; {{ year }} TwibbonCraft. All rights reserved.
      </div>
      <div class="flex items-center gap-2 text-xs font-bold text-slate-400 dark:text-white/30 bg-slate-100 dark:bg-white/5 px-3 py-1.5 rounded-full">
        <Icon icon="ph:shield-check-fill" class="text-green-500" />
        Data stored locally on your device
      </div>
    </div>
  </footer>
</template>

<script setup>
import { Icon } from '@iconify/vue'
const year = new Date().getFullYear()
</script>
