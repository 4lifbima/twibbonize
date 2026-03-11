<template>
  <header
    class="fixed top-0 left-0 right-0 z-50 px-6 transition-all duration-300"
    :class="scrolled ? 'bg-white/80 dark:bg-[#070711]/80 backdrop-blur-xl shadow-sm dark:shadow-[0_1px_0_rgba(255,255,255,0.05)]' : 'bg-transparent py-2'"
  >
    <div class="max-w-6xl mx-auto h-[72px] flex items-center justify-between gap-8">
      <!-- Logo -->
      <div class="flex items-center gap-2.5 flex-shrink-0 group">
        <div class="w-10 h-10 rounded-xl bg-gradient-to-br from-violet-600 to-pink-500 flex items-center justify-center text-white shadow-lg group-hover:scale-105 group-hover:rotate-6 transition-all duration-300">
          <Icon icon="ph:magic-wand-fill" class="w-6 h-6" />
        </div>
        <span class="font-sans text-xl font-extrabold text-slate-900 dark:text-white tracking-tight">
          Twibbon<span class="text-violet-600 dark:text-gold">Craft</span>
        </span>
      </div>

      <!-- Right Actions (Theme) -->
      <div class="flex items-center gap-4">
        <button
          @click="toggleTheme"
          class="w-10 h-10 flex items-center justify-center rounded-full bg-slate-100 hover:bg-slate-200 text-slate-600 dark:bg-white/5 dark:hover:bg-white/15 dark:text-white/80 transition-all border-none cursor-pointer"
          aria-label="Toggle Theme"
        >
          <Icon :icon="isDark ? 'ph:sun-fill' : 'ph:moon-fill'" class="w-5 h-5" />
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

function onScroll() { scrolled.value = window.scrollY > 10 }

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
