<template>
  <footer class="border-t border-slate-200/60 dark:border-white/[0.03] px-6 pt-16 pb-8 mt-auto bg-slate-50 dark:bg-[#070711] transition-colors duration-300 relative overflow-hidden">
    
    <div class="max-w-6xl mx-auto flex flex-wrap justify-between items-start gap-12 relative z-10">
      <!-- Brand -->
      <div class="max-w-xs xl:max-w-sm">
        <div class="flex items-center gap-3 flex-shrink-0 mb-5 group inline-flex cursor-pointer">
          <div class="w-9 h-9 rounded-xl bg-[#632bfc] flex items-center justify-center text-white shadow-[0_4px_15px_rgba(99,43,252,0.3)] group-hover:scale-105 transition-transform">
            <Icon icon="ph:magic-wand-fill" class="w-5 h-5" />
          </div>
          <span class="font-sans text-2xl font-extrabold text-slate-900 dark:text-white tracking-tight">
            Twibbon<span class="text-[#632bfc]">Craft</span>
          </span>
        </div>
        <p class="text-sm font-medium text-slate-500 dark:text-white/50 leading-loose mb-6">
          Create, customize, and share stunning premium twibbon frames that perfectly express who you are.
        </p>
      </div>
      
    </div>
    
    <div class="max-w-6xl mx-auto mt-16 pt-8 border-t border-slate-200/60 dark:border-white/[0.04] flex flex-col md:flex-row items-center justify-between gap-6 relative z-10">
      <div class="text-xs font-bold text-slate-400 dark:text-white/40 tracking-wider uppercase">
        &copy; {{ year }} TwibbonCraft. All rights reserved.
      </div>
      <div class="flex items-center gap-2.5 text-xs font-bold text-slate-600 dark:text-white/60 bg-white dark:bg-white/5 border border-slate-200 dark:border-white/10 px-4 py-2 rounded-full shadow-sm">
        <span class="relative flex h-2.5 w-2.5">
          <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-green-400 opacity-75"></span>
          <span class="relative inline-flex rounded-full h-2.5 w-2.5 bg-green-500"></span>
        </span>
        Local Processing & Privacy First
      </div>
    </div>
  </footer>
</template>

<script setup>
import { Icon } from '@iconify/vue'
const year = new Date().getFullYear()
</script>
