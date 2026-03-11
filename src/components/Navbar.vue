<template>
  <header
    class="fixed top-0 left-0 right-0 z-50 px-4 md:px-6 transition-all duration-500"
    :class="scrolled ? 'bg-white/70 dark:bg-[#070711]/60 backdrop-blur-2xl shadow-[0_8px_30px_rgba(0,0,0,0.03)] dark:shadow-[0_8px_30px_rgba(0,0,0,0.2)] border-b border-slate-200/50 dark:border-white/5' : 'bg-transparent py-3 md:py-5 border-b border-transparent'"
  >
    <div class="max-w-6xl mx-auto h-[64px] flex items-center justify-between gap-8">
      <!-- Logo -->
      <div class="flex items-center gap-3 flex-shrink-0 group cursor-pointer">
        <div class="w-10 h-10 rounded-xl bg-[#632bfc] flex items-center justify-center text-white shadow-[0_4px_15px_rgba(99,43,252,0.3)] group-hover:shadow-[0_8px_25px_rgba(99,43,252,0.5)] group-hover:scale-105 group-hover:rotate-6 transition-all duration-300 relative overflow-hidden">
          <div class="absolute inset-0 bg-white opacity-0 group-hover:opacity-20 transition-opacity"></div>
          <Icon icon="ph:magic-wand-fill" class="w-6 h-6 relative z-10" />
        </div>
        <span class="font-sans text-xl font-extrabold text-slate-900 dark:text-white tracking-tight">
          Twibbon<span class="text-[#632bfc]">Craft</span>
        </span>
      </div>

      <!-- Right Actions (Theme) -->
      <div class="flex items-center gap-4">
        <button
          @click="toggleTheme"
          class="w-11 h-11 flex items-center justify-center rounded-full bg-slate-50 hover:bg-white dark:bg-white/5 dark:hover:bg-white/10 border border-slate-200/50 dark:border-white/10 text-slate-600 dark:text-white/80 transition-all cursor-pointer shadow-sm hover:shadow-md dark:shadow-none relative group"
          aria-label="Toggle Theme"
        >
          <div class="absolute inset-0 rounded-full opacity-0 group-hover:opacity-100 dark:bg-white/5 transition-opacity"></div>
          <Icon :icon="isDark ? 'ph:sun-fill' : 'ph:moon-fill'" class="w-5 h-5 relative z-10" />
        </button>
      </div>
    </div>
  </header>
</template>

<script setup>
import { ref, onMounted, onUnmounted, watch } from 'vue'
import { Icon } from '@iconify/vue'

const scrolled = ref(false)
const isDark = ref(true)

function onScroll() { scrolled.value = window.scrollY > 20 }

onMounted(() => {
  window.addEventListener('scroll', onScroll)
  
  if (localStorage.theme === 'light' || (!('theme' in localStorage) && window.matchMedia('(prefers-color-scheme: light)').matches)) {
    isDark.value = false;
    document.documentElement.classList.remove('dark');
  } else {
    isDark.value = true;
    document.documentElement.classList.add('dark');
  }
})

onUnmounted(() => window.removeEventListener('scroll', onScroll))

function toggleTheme() {
  isDark.value = !isDark.value;
  if (isDark.value) {
    document.documentElement.classList.add('dark');
    localStorage.theme = 'dark';
  } else {
    document.documentElement.classList.remove('dark');
    localStorage.theme = 'light';
  }
}
</script>
