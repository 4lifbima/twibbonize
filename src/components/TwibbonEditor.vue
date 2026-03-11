<template>
  <div class="grid grid-cols-1 md:grid-cols-[1fr_360px] gap-8 items-start max-w-6xl mx-auto w-full">

    <!-- Canvas Area -->
    <div class="flex flex-col items-center gap-6 bg-white/60 dark:bg-[#0d0d1c]/40 backdrop-blur-xl p-6 md:p-10 rounded-md shadow-[0_8px_30px_rgba(0,0,0,0.04)] dark:shadow-[0_8px_30px_rgba(0,0,0,0.2)] border border-[#9c6ffd] dark:border-[#632bfc]/10 transition-colors duration-300 relative overflow-hidden">
      <!-- Decorative background glow -->
      <div class="absolute -top-40 -left-40 w-80 h-80 bg-[#632bfc] rounded-full mix-blend-multiply filter blur-[120px] opacity-10 dark:opacity-20 pointer-events-none"></div>
      <div class="absolute -bottom-40 -right-40 w-80 h-80 bg-[#632bfc] rounded-full mix-blend-multiply filter blur-[120px] opacity-10 dark:opacity-20 pointer-events-none"></div>

      <div
        ref="containerRef"
        class="relative w-full max-w-[500px] rounded-md overflow-hidden shadow-2xl dark:shadow-[0_0_0_1px_rgba(255,255,255,0.06),0_25px_60px_rgba(0,0,0,0.5),0_0_80px_rgba(99,43,252,0.15)] bg-slate-100 dark:bg-[#0a0a18] transition-all duration-300 z-10"
        :class="hasPhoto ? 'ring-2 ring-white/50 dark:ring-[#632bfc]/30' : ''"
      >
        <canvas
          ref="canvasRef"
          :width="CANVAS_WIDTH"
          :height="CANVAS_HEIGHT"
          class="block w-full h-auto select-none"
          :class="hasPhoto && !isDragging ? 'cursor-grab' : isDragging ? 'cursor-grabbing' : 'cursor-default'"
          @mousedown="startDrag"
          @mousemove="onMouseMove"
          @mouseup="endDrag"
          @mouseleave="endDrag"
          @wheel.prevent="onWheel"
          @touchstart.prevent="onTouchStart"
          @touchmove.prevent="onTouchMove"
          @touchend.prevent="onTouchEnd"
        />
        <!-- Upload overlay -->
        <label
          v-if="!hasPhoto"
          for="photo-input"
          class="absolute inset-0 flex items-center justify-center cursor-pointer bg-white/40 dark:bg-[#0a0a18]/40 backdrop-blur-sm hover:bg-white/60 dark:hover:bg-[#0a0a18]/60 transition-all group"
        >
          <div class="text-center text-slate-600 dark:text-white/70 px-6 transform group-hover:scale-105 transition-transform duration-300">
            <div class="flex justify-center mb-4">
              <div class="w-20 h-20 rounded-full bg-[#632bfc]/10 dark:bg-[#632bfc]/20 flex items-center justify-center text-[#632bfc] shadow-lg ring-2 ring-[#632bfc]/20 group-hover:bg-[#632bfc]/20 group-hover:ring-[#632bfc]/40 transition-all duration-300">
                <Icon icon="ph:upload-simple-bold" class="w-9 h-9" />
              </div>
            </div>
            <p class="text-xl font-bold text-slate-800 dark:text-white/90 mb-2">Upload Your Photo</p>
            <span class="text-sm font-medium text-slate-500 dark:text-white/50 bg-slate-200/50 dark:bg-black/20 px-3 py-1 rounded-full">JPG, PNG, WEBP - up to 15 MB</span>
          </div>
        </label>
      </div>
      <p v-if="hasPhoto" class="flex items-center gap-2 text-sm font-medium text-slate-500 dark:text-white/40 text-center bg-white/80 dark:bg-white/5 px-5 py-2.5 rounded-full mt-2 transition-colors duration-300 shadow-sm dark:shadow-none border border-slate-200/50 dark:border-white/5 z-10">
        <Icon icon="ph:hand-grabbing-fill" class="text-[#632bfc] w-4 h-4" />
        Drag to pan • Scroll/pinch to zoom
      </p>
    </div>

    <!-- Controls Panel -->
    <div class="flex flex-col gap-6 bg-white/80 dark:bg-[#0d0d1c]/60 backdrop-blur-xl shadow-[0_8px_30px_rgba(0,0,0,0.04)] dark:shadow-[0_8px_30px_rgba(0,0,0,0.2)] border border-[#9c6ffd] dark:border-white/5  rounded-md p-6 md:p-8 md:sticky md:top-[150px] transition-colors duration-300 relative overflow-hidden">
      <!-- Subtle top inner glow -->
      <div class="absolute top-0 left-0 right-0 h-32 bg-gradient-to-b from-[#632bfc]/5 to-transparent pointer-events-none"></div>

      <!-- Photo Control -->
      <div class="flex flex-col gap-3">
        <div class="flex items-center justify-between">
          <span class="text-xs font-bold uppercase tracking-widest text-[#632bfc]/70 dark:text-[#9c6ffd]/70 flex items-center gap-1.5">
            <Icon icon="ph:image-square-fill" /> Photo
          </span>
          <button v-if="hasPhoto" @click="removePhoto" class="text-xs font-bold text-red-500 hover:text-red-600 dark:text-red-400/80 dark:hover:text-red-400 flex items-center gap-1 bg-red-50 dark:bg-red-500/10 px-2 py-1 rounded-md transition-colors cursor-pointer border-none">
            <Icon icon="ph:trash-fill" /> Remove
          </button>
        </div>
        <label
          for="photo-input"
          class="group flex flex-col items-center justify-center gap-3 py-6 px-4 bg-slate-50 hover:bg-[#632bfc]/5 dark:bg-[#632bfc]/5 border-2 border-dashed border-slate-200 hover:border-[#632bfc]/50 dark:border-[#632bfc]/30 rounded-2xl text-[#632bfc] dark:text-[#9c6ffd] text-sm font-bold cursor-pointer dark:hover:bg-[#632bfc]/10 transition-all z-10"
        >
          <div class="w-12 h-12 rounded-full bg-white dark:bg-[#632bfc]/20 flex items-center justify-center shadow-sm group-hover:scale-110 transition-transform duration-300">
            <Icon :icon="hasPhoto ? 'ph:arrows-clockwise-bold' : 'ph:camera-plus-bold'" class="w-6 h-6" />
          </div>
          {{ hasPhoto ? 'Change Photo' : 'Upload New Photo' }}
        </label>
        <input id="photo-input" ref="fileInputRef" type="file" accept="image/*" @change="handlePhotoUpload" class="hidden" />
      </div>

      <div v-if="hasPhoto" class="h-px w-full bg-slate-100 dark:bg-white/5"></div>

      <!-- Zoom -->
      <div v-if="hasPhoto" class="flex flex-col gap-3">
        <div class="flex items-center justify-between">
          <span class="text-xs font-bold uppercase tracking-widest text-[#632bfc]/70 dark:text-[#9c6ffd]/70 flex items-center gap-1.5">
            <Icon icon="ph:magnifying-glass-plus-fill" /> Zoom
          </span>
          <span class="text-xs font-bold text-[#632bfc] dark:text-[#9c6ffd] bg-[#632bfc]/5 dark:bg-[#632bfc]/20 px-2 py-0.5 rounded-md">{{ Math.round(zoom * 100) }}%</span>
        </div>
        <div class="flex items-center gap-3">
          <button @click="zoom = Math.max(0.5, zoom - 0.15); render()" class="w-8 h-8 flex items-center justify-center rounded-full bg-slate-100 hover:bg-[#632bfc] dark:bg-white/10 dark:hover:bg-[#632bfc] text-slate-600 hover:text-white dark:text-white/70 dark:hover:text-white text-lg cursor-pointer transition-all border-none">
            <Icon icon="ph:minus-bold" />
          </button>
          <input type="range" v-model.number="zoom" min="0.5" max="4" step="0.05" @input="render" class="flex-1 h-2 rounded-full appearance-none bg-slate-200 dark:bg-white/10 accent-[#632bfc] cursor-pointer" />
          <button @click="zoom = Math.min(4, zoom + 0.15); render()" class="w-8 h-8 flex items-center justify-center rounded-full bg-slate-100 hover:bg-[#632bfc] dark:bg-white/10 dark:hover:bg-[#632bfc] text-slate-600 hover:text-white dark:text-white/70 dark:hover:text-white text-lg cursor-pointer transition-all border-none">
            <Icon icon="ph:plus-bold" />
          </button>
        </div>
      </div>

      <div class="h-px w-full bg-slate-100 dark:bg-white/5"></div>

      <!-- Caption -->
      <div class="flex flex-col gap-3">
        <div class="flex items-center justify-between">
          <span class="text-xs font-bold uppercase tracking-widest text-[#632bfc]/70 dark:text-[#9c6ffd]/70 flex items-center gap-1.5">
            <Icon icon="ph:text-t-fill" /> Caption
          </span>
          <span class="text-xs font-bold text-slate-400 dark:text-white/30">{{ caption.length }}/60</span>
        </div>
        <div class="relative z-10">
          <input
            v-model="caption"
            type="text"
            maxlength="60"
            placeholder="Type your caption here..."
            @input="render"
            class="w-full pl-12 pr-4 py-4 bg-white dark:bg-[#0a0a18] border border-slate-200 dark:border-white/10 rounded-2xl text-slate-800 dark:text-[#f0f0ff] text-sm font-medium placeholder-slate-400 dark:placeholder-white/30 outline-none focus:border-[#632bfc] focus:ring-4 focus:ring-[#632bfc]/10 dark:focus:border-[#632bfc]/60 transition-all duration-300 shadow-sm inset-y-0"
          />
          <div class="absolute left-3 top-1/2 -translate-y-1/2 w-8 h-8 rounded-full bg-slate-50 dark:bg-white/5 flex items-center justify-center">
            <Icon icon="ph:magic-wand-fill" class="text-slate-400 dark:text-white/40 w-4 h-4" />
          </div>
        </div>
      </div>

      <!-- Caption Style -->
      <div v-if="caption" class="flex flex-col gap-4 bg-slate-50 dark:bg-[#632bfc]/[0.03] p-4 rounded-2xl border border-slate-100 dark:border-[#632bfc]/10">
        <span class="text-xs font-bold uppercase tracking-widest text-[#632bfc]/70 dark:text-[#9c6ffd]/70">Caption Style</span>
        
        <div class="flex items-center gap-3">
          <span class="text-xs font-bold text-slate-500 dark:text-white/60 w-10">Size</span>
          <input type="range" v-model.number="captionSize" min="14" max="42" step="1" @input="render" class="flex-1 h-1.5 rounded-full appearance-none bg-slate-200 dark:bg-white/10 accent-[#632bfc] cursor-pointer" />
          <span class="text-xs font-bold text-slate-500 dark:text-white/60 w-8 text-right">{{ captionSize }}</span>
        </div>
        
        <div class="flex items-center gap-3">
          <span class="text-xs font-bold text-slate-500 dark:text-white/60 w-10">Color</span>
          <div class="flex gap-2 flex-wrap flex-1">
            <button
              v-for="c in colorSwatches"
              :key="c"
              :style="{ background: c }"
              @click="captionColor = c; render()"
              class="w-6 h-6 rounded-full border-2 cursor-pointer transition-all duration-200 shadow-sm"
              :class="captionColor === c ? 'border-[#632bfc] scale-125 shadow-[0_0_0_2px_rgba(99,43,252,0.25)]' : 'border-black/10 dark:border-white/20 hover:scale-110'"
            />
          </div>
        </div>
      </div>

      <!-- Download -->
      <div class="flex flex-col gap-2 mt-2">
        <button
          :disabled="!hasPhoto"
          @click="downloadImage"
          class="group flex items-center justify-center gap-2 w-full py-4 rounded-xl text-base font-bold tracking-wide transition-all duration-300 border-none"
          :class="hasPhoto
            ? 'bg-[#632bfc] text-white shadow-[0_4px_20px_rgba(99,43,252,0.35)] hover:bg-[#5520e8] hover:shadow-[0_8px_32px_rgba(99,43,252,0.55)] hover:-translate-y-1 cursor-pointer'
            : 'bg-slate-100 dark:bg-white/5 text-slate-400 dark:text-white/30 cursor-not-allowed'"
        >
          <Icon icon="ph:download-simple-bold" class="w-5 h-5 group-hover:animate-bounce" v-if="hasPhoto" />
          <Icon icon="ph:lock-key-fill" class="w-5 h-5" v-else />
          Download Twibbon
        </button>
        <p v-if="!hasPhoto" class="text-xs font-medium text-slate-400 dark:text-white/40 text-center mt-2">
          Please upload a photo first to download
        </p>
      </div>

    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'
import { Icon } from '@iconify/vue'

const props = defineProps({
  frameData: { type: String, required: true },
  campaignTitle: { type: String, default: '' }
})

const CANVAS_WIDTH = 1080
const CANVAS_HEIGHT = 1350
const canvasRef = ref(null)
const containerRef = ref(null)
const fileInputRef = ref(null)

const hasPhoto = ref(false)
const zoom = ref(1)
const panX = ref(0)
const panY = ref(0)
const caption = ref('')
const captionSize = ref(25)
const captionColor = ref('#ffffff')
const isDragging = ref(false)

let dragStart = { x: 0, y: 0 }
let touchState = { lastX: 0, lastY: 0, pinchDist: 0 }
let photoImg = null
let frameImg = null

const colorSwatches = ['#ffffff', '#f1f5f9', '#fce7f3', '#fef08a', '#632bfc', '#6ee7b7', '#fca5a5', '#0f172a']

onMounted(async () => { await loadFrameImage(); render() })
watch(() => props.frameData, async () => { await loadFrameImage(); render() })

function loadFrameImage() {
  return new Promise(resolve => {
    const img = new Image()
    img.onload = () => { frameImg = img; resolve() }
    img.onerror = resolve
    img.src = props.frameData
  })
}

function render() {
  const canvas = canvasRef.value
  if (!canvas) return
  const ctx = canvas.getContext('2d')
  ctx.clearRect(0, 0, CANVAS_WIDTH, CANVAS_HEIGHT)
  
  // Clean canvas bg, adaptable checkboard or solid color for editor
  ctx.fillStyle = '#e2e8f0' // Slate 200 roughly
  ctx.fillRect(0, 0, CANVAS_WIDTH, CANVAS_HEIGHT)

  if (photoImg) {
    const scale = Math.max(CANVAS_WIDTH / photoImg.naturalWidth, CANVAS_HEIGHT / photoImg.naturalHeight) * zoom.value
    const w = photoImg.naturalWidth * scale
    const h = photoImg.naturalHeight * scale
    ctx.drawImage(photoImg, (CANVAS_WIDTH - w) / 2 + panX.value, (CANVAS_HEIGHT - h) / 2 + panY.value, w, h)
  } else {
    // Elegant gradient if no photo
    const grad = ctx.createLinearGradient(0, 0, CANVAS_WIDTH, CANVAS_HEIGHT)
    grad.addColorStop(0, '#f8fafc')
    grad.addColorStop(1, '#e2e8f0')
    
    // Fallback dark gradient logically if we can check document class, but here statically we just use light/neutral since user exports it. Usually the image has a transparent hole.
    ctx.fillStyle = grad
    ctx.fillRect(0, 0, CANVAS_WIDTH, CANVAS_HEIGHT)
  }

  // Draw Frame
  if (frameImg) ctx.drawImage(frameImg, 0, 0, CANVAS_WIDTH, CANVAS_HEIGHT)

  // Draw Caption
  if (caption.value.trim()) {
    const fontSize = captionSize.value
    ctx.font = `800 ${fontSize}px "Plus Jakarta Sans", sans-serif`
    ctx.textAlign = 'center'
    ctx.textBaseline = 'alphabetic'
    const textY = CANVAS_HEIGHT * 0.915
    
    // Nice text drop shadow for readability
    ctx.shadowColor = 'rgba(0,0,0,0.85)'
    ctx.shadowBlur = 10
    ctx.shadowOffsetY = 3
    
    // Outline for clarity
    ctx.lineWidth = fontSize * 0.15
    ctx.strokeStyle = 'rgba(0,0,0,0.6)'
    ctx.lineJoin = 'round'
    ctx.strokeText(caption.value, CANVAS_WIDTH / 2, textY)
    
    // Fill text
    ctx.fillStyle = captionColor.value
    ctx.fillText(caption.value, CANVAS_WIDTH / 2, textY)
    
    // Reset shadow
    ctx.shadowColor = 'transparent'
    ctx.shadowBlur = 0
    ctx.shadowOffsetY = 0
  }
}

function handlePhotoUpload(e) {
  const file = e.target.files[0]
  if (!file) return
  if (file.size > 15 * 1024 * 1024) { alert('File too large. Please choose an image under 15 MB.'); return }
  const reader = new FileReader()
  reader.onload = evt => {
    const img = new Image()
    img.onload = () => { photoImg = img; hasPhoto.value = true; zoom.value = 1; panX.value = 0; panY.value = 0; render() }
    img.src = evt.target.result
  }
  reader.readAsDataURL(file)
  e.target.value = ''
}

function removePhoto() {
  photoImg = null; hasPhoto.value = false; zoom.value = 1; panX.value = 0; panY.value = 0; render()
}

function getCanvasScale() {
  const c = canvasRef.value
  return c ? CANVAS_WIDTH / c.getBoundingClientRect().width : 1
}

function startDrag(e) { if (!hasPhoto.value) return; isDragging.value = true; dragStart = { x: e.clientX, y: e.clientY } }
function onMouseMove(e) {
  if (!isDragging.value) return
  const s = getCanvasScale()
  panX.value += (e.clientX - dragStart.x) * s
  panY.value += (e.clientY - dragStart.y) * s
  dragStart = { x: e.clientX, y: e.clientY }
  render()
}
function endDrag() { isDragging.value = false }
function onWheel(e) {
  if (!hasPhoto.value) return
  zoom.value = Math.min(Math.max(zoom.value + (e.deltaY > 0 ? -0.1 : 0.1), 0.5), 4)
  render()
}

function getTouchDist(t) { return Math.hypot(t[0].clientX - t[1].clientX, t[0].clientY - t[1].clientY) }
function onTouchStart(e) {
  if (!hasPhoto.value) return
  if (e.touches.length === 1) { touchState.lastX = e.touches[0].clientX; touchState.lastY = e.touches[0].clientY; isDragging.value = true }
  else if (e.touches.length === 2) { touchState.pinchDist = getTouchDist(e.touches); isDragging.value = false }
}
function onTouchMove(e) {
  if (!hasPhoto.value) return
  const s = getCanvasScale()
  if (e.touches.length === 1 && isDragging.value) {
    panX.value += (e.touches[0].clientX - touchState.lastX) * s
    panY.value += (e.touches[0].clientY - touchState.lastY) * s
    touchState.lastX = e.touches[0].clientX; touchState.lastY = e.touches[0].clientY
    render()
  } else if (e.touches.length === 2) {
    const d = getTouchDist(e.touches)
    if (touchState.pinchDist > 0) { zoom.value = Math.min(Math.max(zoom.value * (d / touchState.pinchDist), 0.5), 4); render() }
    touchState.pinchDist = d
  }
}
function onTouchEnd() { isDragging.value = false; touchState.pinchDist = 0 }

function downloadImage() {
  const canvas = canvasRef.value
  if (!canvas) return
  render()
  const link = document.createElement('a')
  link.download = `twibbon-${props.campaignTitle.replace(/\s+/g, '-').toLowerCase() || 'frame'}.png`
  link.href = canvas.toDataURL('image/png', 1.0)
  link.click()
}

defineExpose({ render, downloadImage })
</script>

<style scoped>
input[type="range"]::-webkit-slider-thumb {
  -webkit-appearance: none;
  width: 18px; height: 18px;
  border-radius: 50%;
  background: white;
  border: 4px solid #632bfc;
  cursor: pointer;
  box-shadow: 0 2px 6px rgba(99,43,252,0.4);
  transition: all 0.2s;
}
input[type="range"]::-webkit-slider-thumb:hover {
  transform: scale(1.1);
  box-shadow: 0 3px 8px rgba(99,43,252,0.6);
}
input[type="range"]::-moz-range-thumb {
  width: 18px; height: 18px;
  border-radius: 50%;
  background: white;
  border: 4px solid #632bfc;
  cursor: pointer;
  box-shadow: 0 2px 6px rgba(99,43,252,0.4);
  transition: all 0.2s;
}
input[type="range"]::-moz-range-thumb:hover {
  transform: scale(1.1);
  box-shadow: 0 3px 8px rgba(99,43,252,0.6);
}
</style>
