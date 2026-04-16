<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import HeroSection      from './components/HeroSection.vue'
import AboutSection     from './components/AboutSection.vue'
import VideoSection     from './components/VideoSection.vue'
import SkillsSection    from './components/SkillsSection.vue'
import PortfolioSection from './components/PortfolioSection.vue'
import ContactSection   from './components/ContactSection.vue'

// Comet tail — ring buffer of last 10 positions
const TAIL_LENGTH = 10
const tail = ref([])
let rafId = null
let mouseX = -999
let mouseY = -999

const onMouseMove = (e) => {
  mouseX = e.clientX
  mouseY = e.clientY
}

const updateTail = () => {
  if (mouseX !== -999) {
    tail.value = [{ x: mouseX, y: mouseY }, ...tail.value].slice(0, TAIL_LENGTH)
  }
  rafId = requestAnimationFrame(updateTail)
}

// Scroll progress
const scrollProgress = ref(0)
const onScroll = () => {
  const total = document.documentElement.scrollHeight - window.innerHeight
  scrollProgress.value = total > 0 ? Math.min((window.scrollY / total) * 100, 100) : 0
}

onMounted(() => {
  window.addEventListener('mousemove', onMouseMove)
  window.addEventListener('scroll', onScroll, { passive: true })
  rafId = requestAnimationFrame(updateTail)
  onScroll()
})

onUnmounted(() => {
  window.removeEventListener('mousemove', onMouseMove)
  window.removeEventListener('scroll', onScroll)
  cancelAnimationFrame(rafId)
})
</script>

<template>
  <!-- Top scroll progress bar -->
  <div
    style="
      position: fixed;
      top: 0; left: 0;
      height: 3px;
      background: var(--color-primary);
      z-index: 99999;
      pointer-events: none;
      transition: width 0.1s linear;
      border-radius: 0 2px 2px 0;
    "
    :style="{ width: scrollProgress + '%' }"
  />

  <!-- Comet tail particles -->
  <div
    v-for="(p, i) in tail"
    :key="i"
    class="comet-dot"
    :style="{
      left: p.x + 'px',
      top:  p.y + 'px',
      width:  (10 - i * 0.8) + 'px',
      height: (10 - i * 0.8) + 'px',
      opacity: (1 - i / TAIL_LENGTH) * 0.85,
      background: `rgba(109, 159, 231, ${1 - i / TAIL_LENGTH})`,
      boxShadow: i < 3 ? `0 0 ${6 - i * 1.5}px 2px rgba(109,159,231,0.5)` : 'none',
    }"
  />

  <div class="grid grid-cols-12">
    <div class="col-span-13"><HeroSection /></div>
    <div class="col-span-12"><AboutSection /></div>
    <div class="col-span-12"><VideoSection /></div>
    <div class="col-span-12"><SkillsSection /></div>
    <div class="col-span-12" style="position: relative; z-index: 1;"><PortfolioSection /></div>
    <div class="col-span-12 contact-wrap" style="position: relative; z-index: 2; margin-top: -100px;"><ContactSection /></div>
  </div>
</template>

<style>
/* Ensure full width on mobile */
html, body { overflow-x: hidden; max-width: 100vw; }

.comet-dot {
  position: fixed;
  border-radius: 50%;
  pointer-events: none;
  z-index: 999997;
  transform: translate(-50%, -50%);
  transition: none;
}

@media (min-width: 769px) and (max-width: 1024px) {
  .contact-wrap { margin-top: -60px !important; }
}

@media (max-width: 768px) {
  .comet-dot { display: none; }
  .contact-wrap { margin-top: 2rem !important; }
}
</style>
