<template>
  <!-- Nav: outside hero section so it's not trapped in its stacking context -->
  <nav class="nav-bar fixed top-0 left-0 right-0 flex justify-end" style="z-index: 9999;">
      <div
        class="flex items-center rounded-full"
        style="
          gap: 0.3em;
          padding: 0.3em;
          background: rgba(255,255,255,0.35);
          backdrop-filter: blur(18px) saturate(180%);
          -webkit-backdrop-filter: blur(18px) saturate(180%);
          border: 1.5px solid rgba(255,255,255,0.6);
          box-shadow: 0 4px 24px rgba(100,160,255,0.15), inset 0 1px 0 rgba(255,255,255,0.7);
          font-size: 1rem;
        "
      >
        <a
          v-for="link in links"
          :key="link.label"
          :href="link.href"
          class="nav-link flex items-center rounded-full transition-all duration-300"
          :style="{
            color:      activeSection === link.id || hoveredLink === link.id ? '#ffffff' : 'var(--color-primary)',
            background: activeSection === link.id ? 'var(--color-primary)' : hoveredLink === link.id ? 'rgba(22, 76, 149, 0.15)' : 'transparent',
          }"
          @click.prevent="scrollTo(link.href)"
          @mouseenter="hoveredLink = link.id"
          @mouseleave="hoveredLink = null"
        >
          <span class="flex-shrink-0 flex items-center justify-center" style="width:2em; height:2em;" v-html="link.icon"></span>
          <span class="nav-label">{{ link.label }}</span>
        </a>
      </div>
  </nav>

  <section id="home" class="relative w-full overflow-hidden" style="height: 100dvh; z-index: 10;">

    <!-- Full-bleed cloud background with parallax -->
    <img
      src="@/assets/images/cloudy.webp"
      alt="Sky background"
      width="1920"
      height="1080"
      class="absolute inset-0 w-full h-full object-cover object-top cloud-drift"
      :style="{ transform: `scale(1.15) translateX(${cloudDriftOffset}%) translateY(${parallaxY}px)` }"
    />

    <!-- Subtle overlay -->
    <div class="absolute inset-0 bg-white/10"></div>

    <!-- Hero content -->
    <div class="absolute inset-0 z-10 flex items-center justify-center hero-content-pad">
      <div class="hero-inner w-full max-w-5xl">

        <!-- Logo -->
        <div class="hero-logo-wrap flex justify-center">
          <div class="hero-logo" style="position: relative; width: 558px; height: 370px;">
            <img
              src="@/assets/images/logo.png"
              alt="MH Logo"
              width="558"
              height="370"
              class="logo-float object-contain"
              style="width: 100%; height: 100%; object-fit: contain;"
            />
            <span v-for="sparkle in sparkles" :key="sparkle.id" class="sparkle" :style="sparkle.style" />
          </div>
        </div>

        <!-- Name + subtitle -->
        <div class="hero-text-wrap flex flex-col">
          <div class="logo-float" style="position: relative; overflow: visible;">
            <span v-for="sparkle in textSparkles" :key="sparkle.id" class="sparkle" :style="sparkle.style" />
            <h1 class="hero-name">
              <span class="word-reveal" style="display: inline-block; animation-delay: 0.7s;"><span class="hero-initial">M</span>ihaela</span>
              <span class="word-reveal" style="display: inline-block; animation-delay: 1.0s;"><span class="hero-initial">H</span>ioara</span>
            </h1>
            <p class="subtitle-reveal hero-subtitle">Multimedia Designer</p>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const activeSection = ref('home')
const hoveredLink   = ref(null)
const parallaxY        = ref(0)
const cloudDriftOffset = ref(0)
let driftStart = null
let driftFrame = null

const animateDrift = (ts) => {
  if (!driftStart) driftStart = ts
  const t = (ts - driftStart) / 20000 // 20s cycle
  cloudDriftOffset.value = Math.sin(t * Math.PI * 2) * -5
  driftFrame = requestAnimationFrame(animateDrift)
}

// Sparkles
const sparkles = ref([])
const textSparkles = ref([])
let sparkleId = 0
let sparkleInterval = null
let textSparkleInterval = null

const makeSparkle = (arr, x, y) => {
  const id = sparkleId++
  const size = Math.random() * 30 + 25
  const duration = Math.random() * 900 + 700

  arr.value.push({
    id,
    style: {
      left: `${x}px`,
      top: `${y}px`,
      width: `${size}px`,
      height: `${size}px`,
      animationDuration: `${duration}ms`,
    },
  })

  setTimeout(() => {
    arr.value = arr.value.filter(s => s.id !== id)
  }, duration)
}

const spawnSparkle     = () => makeSparkle(sparkles,     Math.random() * 520 - 10, Math.random() * 330 - 10)
const spawnTextSparkle = () => makeSparkle(textSparkles, Math.random() * 560,      Math.random() * 200 - 10)

const links = [
  { label: 'Home',      href: '#home',      id: 'home',      icon: `<svg xmlns="http://www.w3.org/2000/svg" width="46" height="46" viewBox="0 0 56 56"><path fill="currentColor" d="M.625 27.824c0 1.125.89 1.805 1.992 1.805c.68 0 1.219-.328 1.688-.797L27.18 7.996c.257-.258.539-.352.843-.352c.282 0 .54.094.82.352l22.852 20.836c.492.469 1.031.797 1.688.797c1.101 0 1.992-.68 1.992-1.805c0-.703-.258-1.148-.703-1.547l-8.11-7.382V5.043c0-1.031-.656-1.687-1.687-1.687h-3.07c-1.008 0-1.711.656-1.711 1.687v7.969l-9.282-8.485C29.992 3.754 28.984 3.38 28 3.38c-.985 0-1.969.375-2.813 1.148L1.328 26.277c-.422.399-.703.844-.703 1.547m6.703 19.664c0 3.258 1.969 5.157 5.273 5.157h9.493V35.98c0-1.078.726-1.78 1.804-1.78h8.274c1.078 0 1.781.702 1.781 1.78v16.665h9.469c3.304 0 5.25-1.899 5.25-5.157V30.332l-19.899-17.93c-.258-.234-.539-.351-.82-.351c-.258 0-.516.117-.797.375L7.328 30.449Z"/></svg>` },
  { label: 'About Me',  href: '#about',     id: 'about',     icon: `<svg xmlns="http://www.w3.org/2000/svg" width="46" height="46" viewBox="0 0 24 24"><g fill="none"><path fill="currentColor" d="M15 15H9a4 4 0 0 0-3.834 2.856A8.98 8.98 0 0 0 12 21a8.98 8.98 0 0 0 6.834-3.144A4 4 0 0 0 15 15" opacity="0.16"/><path stroke="currentColor" stroke-width="2" d="M21 12a8.96 8.96 0 0 1-1.526 5.016A8.99 8.99 0 0 1 12 21a8.99 8.99 0 0 1-7.474-3.984A9 9 0 1 1 21 12Z"/><path fill="currentColor" d="M13 9a1 1 0 0 1-1 1v2a3 3 0 0 0 3-3zm-1 1a1 1 0 0 1-1-1H9a3 3 0 0 0 3 3zm-1-1a1 1 0 0 1 1-1V6a3 3 0 0 0-3 3zm1-1a1 1 0 0 1 1 1h2a3 3 0 0 0-3-3zm-6.834 9.856l-.959-.285l-.155.523l.355.413zm13.668 0l.76.651l.354-.413l-.155-.523zM9 16h6v-2H9zm0-2a5 5 0 0 0-4.793 3.571l1.917.57A3 3 0 0 1 9 16zm3 6a7.98 7.98 0 0 1-6.075-2.795l-1.518 1.302A9.98 9.98 0 0 0 12 22zm3-4c1.357 0 2.506.902 2.876 2.142l1.916-.571A5 5 0 0 0 15 14zm3.075 1.205A7.98 7.98 0 0 1 12 20v2a9.98 9.98 0 0 0 7.593-3.493z"/></g></svg>` },
  { label: 'Portfolio', href: '#portfolio', id: 'portfolio', icon: `<svg xmlns="http://www.w3.org/2000/svg" width="46" height="46" viewBox="0 0 20 20"><path fill="currentColor" d="M4 5H.78c-.37 0-.74.32-.69.84l1.56 9.99S3.5 8.47 3.86 6.7c.11-.53.61-.7.98-.7H10s-.7-2.08-.77-2.31C9.11 3.25 8.89 3 8.45 3H5.14c-.36 0-.7.23-.8.64C4.25 4.04 4 5 4 5m4.88 0h-4s.42-1 .87-1h2.13c.48 0 1 1 1 1M2.67 16.25c-.31.47-.76.75-1.26.75h15.73c.54 0 .92-.31 1.03-.83c.44-2.19 1.68-8.44 1.68-8.44c.07-.5-.3-.73-.62-.73H16V5.53c0-.16-.26-.53-.66-.53h-3.76c-.52 0-.87.58-.87.58L10 7H5.59c-.32 0-.63.19-.69.5c0 0-1.59 6.7-1.72 7.33c-.07.37-.22.99-.51 1.42M15.38 7H11s.58-1 1.13-1h2.29c.71 0 .96 1 .96 1"/></svg>` },
  { label: 'Contact',   href: '#contact',   id: 'contact',   icon: `<svg xmlns="http://www.w3.org/2000/svg" width="46" height="46" viewBox="0 0 24 24"><path fill="currentColor" d="M9.004 3.416C8.432 2.606 7.64 2.241 6.8 2.25c-.797.008-1.573.349-2.221.803A6.2 6.2 0 0 0 2.92 4.79c-.41.649-.706 1.416-.666 2.165c.193 3.603 2.22 7.453 5.067 10.302c2.845 2.846 6.644 4.824 10.48 4.446c.752-.074 1.463-.457 2.044-.945a5.8 5.8 0 0 0 1.443-1.84c.34-.692.543-1.49.431-2.267c-.116-.81-.569-1.534-1.402-2.014a16 16 0 0 1-.512-.31c-.15-.093-.31-.194-.504-.31a7.5 7.5 0 0 0-1.249-.618c-.447-.163-.958-.27-1.49-.197c-.551.076-1.063.336-1.506.802c-.341.36-.843.472-1.549.268c-.718-.208-1.526-.724-2.228-1.422c-.702-.696-1.233-1.51-1.46-2.245c-.224-.728-.125-1.263.225-1.632c.473-.498.725-1.052.778-1.638c.052-.57-.09-1.106-.293-1.574c-.304-.699-.82-1.394-1.224-1.936a22 22 0 0 1-.3-.41"/></svg>` },
]

// Smooth scroll on click
const scrollTo = (href) => {
  const id = href.replace('#', '')
  const el = document.getElementById(id)
  if (el) {
    el.scrollIntoView({ behavior: 'smooth' })
  }
}

// Update active nav + parallax on scroll
const handleScroll = () => {
  parallaxY.value = window.scrollY * 0.35

  const sections = ['home', 'about', 'portfolio', 'contact']
  for (let i = sections.length - 1; i >= 0; i--) {
    const el = document.getElementById(sections[i])
    if (el) {
      const rect = el.getBoundingClientRect()
      if (rect.top <= window.innerHeight / 2) {
        activeSection.value = sections[i]
        return
      }
    }
  }
  activeSection.value = 'home'
}

onMounted(() => {
  window.addEventListener('scroll', handleScroll, { passive: true })
  driftFrame = requestAnimationFrame(animateDrift)
  // Start sparkles after logo has faded in
  setTimeout(() => {
    sparkleInterval     = setInterval(spawnSparkle,     350)
    textSparkleInterval = setInterval(spawnTextSparkle, 400)
  }, 1200)
})

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
  clearInterval(sparkleInterval)
  clearInterval(textSparkleInterval)
  cancelAnimationFrame(driftFrame)
})
</script>

<style scoped>
.cloud-drift {
  transform-origin: center top;
}

.nav-link {
  gap: 0.5em;
  padding: 0.55em 1.3em;
  font-family: 'Montserrat Alternates', sans-serif;
  font-weight: 700;
  font-size: 1em;
  white-space: nowrap;
}

@keyframes fade-slide-up {
  from { opacity: 0; transform: translateY(40px); }
  to   { opacity: 1; transform: translateY(0); }
}

.hero-logo {
  opacity: 0;
  animation: fade-slide-up 0.9s ease forwards 0.2s;
}

.logo-float {
  animation: logo-float 4s ease-in-out infinite 1.2s;
}

@keyframes logo-float {
  0%, 100% { transform: translateY(0); }
  50%       { transform: translateY(-16px); }
}

.word-reveal {
  opacity: 0;
  animation: fade-slide-up 0.7s ease forwards;
}

.subtitle-reveal {
  opacity: 0;
  animation: fade-slide-up 0.7s ease forwards;
  animation-delay: 1.3s;
}

/* ── Responsive ── */
.nav-bar       { padding: 2.8em 6em 0 0; }
.hero-content-pad { padding: 10vh 6vw 4vh; }
.hero-inner    { display: grid; grid-template-columns: 5fr 7fr; align-items: end; gap: 2vw; }
.hero-logo-wrap { margin-left: -180px; margin-top: -30px; }
.hero-text-wrap { margin-left: -80px; gap: 0.9vh; }
.hero-name     { font-family: 'Poppins', sans-serif; font-weight: 700; font-style: italic; color: var(--color-primary); font-size: 100px; line-height: 0.45; white-space: nowrap; }
.hero-initial  { font-size: 1.2em; }
.hero-subtitle { font-family: 'Montserrat Alternates', sans-serif; font-weight: 400; color: var(--color-secondary); font-size: 60px; white-space: nowrap; }

@media (min-width: 769px) and (max-width: 1024px) {
  .nav-bar { padding: 1.6em 2.5em 0 0; }
  .hero-content-pad { padding: 8vh 4vw 4vh; }
  .hero-inner { gap: 1vw; }
  .hero-logo-wrap { margin-left: -60px; margin-top: -20px; }
  .hero-logo { width: clamp(260px, 32vw, 360px) !important; height: auto !important; }
  .hero-text-wrap { margin-left: -30px; }
  .hero-name { font-size: clamp(52px, 7vw, 68px); }
  .hero-subtitle { font-size: clamp(30px, 4vw, 40px); }
}

@media (max-width: 768px) {
  .nav-bar { padding: 1em 1em 0; justify-content: center; }
  .nav-label { display: none; }
  .nav-link { padding: 0.5em 0.6em !important; }

  .hero-content-pad { padding: 12vh 4vw 4vh; }
  .hero-inner { grid-template-columns: 1fr; grid-template-rows: auto auto; justify-items: center; gap: 0; }
  .hero-logo-wrap { margin-left: -30px; margin-top: 0; }
  .hero-logo { width: 240px !important; height: 160px !important; }
  .hero-text-wrap { margin-left: 0; align-items: center; text-align: center; }
  .hero-name { font-size: clamp(22px, 7.5vw, 44px); white-space: nowrap; text-align: center; line-height: 1.0; }
  .hero-name .word-reveal { display: inline-block; }
  .hero-subtitle { font-size: clamp(14px, 4.5vw, 22px); white-space: nowrap; text-align: center; }
}

/* Sparkles */
@keyframes sparkle-pop {
  0%   { opacity: 0; transform: scale(0) rotate(0deg); }
  40%  { opacity: 1; transform: scale(1) rotate(45deg); }
  100% { opacity: 0; transform: scale(0) rotate(90deg); }
}

.sparkle {
  position: absolute;
  pointer-events: none;
  clip-path: polygon(50% 0%, 52% 48%, 100% 50%, 52% 52%, 50% 100%, 48% 52%, 0% 50%, 48% 48%);
  background: rgba(255, 255, 255, 0.95);
  filter: drop-shadow(0 0 8px rgba(255, 255, 255, 1)) drop-shadow(0 0 16px rgba(255, 255, 255, 0.7));
  animation: sparkle-pop linear forwards;
}
</style>