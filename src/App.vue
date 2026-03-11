<script setup>
import { ref } from 'vue'
import Navbar from './components/Navbar.vue'
import Footer from './components/Footer.vue'
import TwibbonEditor from './components/TwibbonEditor.vue'

const editorRef = ref(null)

</script>

<template>
  <div class="flex flex-col min-h-screen bg-slate-50 dark:bg-app-bg text-[#f0f0ff] transition-colors duration-300">
    <Navbar />
    
    <main class="flex-1 pt-[72px]">

      <!-- Editor Canvas -->
      <div class="p-6 md:py-12">
        <div class="max-w-6xl mx-auto">
          <TwibbonEditor
            ref="editorRef"
            frame-data="/frame.png"
            campaign-title="Custom Frame"
          />
        </div>
      </div>

    </main>
    
    <Footer />
  </div>
</template>
