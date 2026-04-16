<template>
  <section id="video" class="py-16 bg-white">
    <div class="grid grid-cols-12 px-6" style="max-width: 1280px; margin: 0 auto; margin-top: 5rem;">

      <!-- Heading -->
      <div class="col-start-2 col-span-10 mb-6" ref="textRef">
        <h2 :style="{
          fontFamily: '\'Poppins\', sans-serif',
          fontWeight: 800,
          fontSize: '60px',
          color: '#164C95',
          lineHeight: 1.1,
          opacity: textVisible ? 1 : 0,
          transform: textVisible ? 'translateX(0)' : 'translateX(-70px)',
          transition: textVisible ? 'opacity 0.7s ease, transform 0.7s ease' : 'none',
        }">Video CV</h2>
        <p :style="{
          fontFamily: '\'Montserrat Alternates\', sans-serif',
          fontWeight: 400,
          fontSize: '1.3rem',
          color: '#6D9FE7',
          marginTop: '0.18rem',
          marginBottom: '3rem',
          opacity: textVisible ? 1 : 0,
          transform: textVisible ? 'translateX(0)' : 'translateX(-70px)',
          transition: textVisible ? 'opacity 0.7s ease, transform 0.7s ease' : 'none',
          transitionDelay: textVisible ? '0.3s' : '0s',
        }">In flesh and bones...and pixels!</p>
      </div>

      <!-- TV frame -->
      <div class="col-start-2 col-span-10 relative" ref="tvRef">
        <div
          :class="tvVisible ? 'tv-frame tv-on' : 'tv-frame'"
          style="
            border-radius: 16px;
            border: 5px solid #9497AB;
            background: #FDFDFD;
            padding: 17px;
          "
        >
          <div :class="tvVisible ? 'video-screen screen-glow' : 'video-screen'" style="
            border-radius: 10px;
            border: 4px solid var(--color-primary);
            overflow: hidden;
            position: relative;
            padding-top: 55.65%;
            background: #000;
          ">
            <!-- Scan lines overlay -->
            <div class="scan-lines" style="position: absolute; inset: 0; pointer-events: none; z-index: 10;" />

            <iframe
              src="https://www.youtube.com/embed/B7Yipf95Mqk"
              title="Video CV"
              frameborder="0"
              allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
              allowfullscreen
              style="
                position: absolute;
                top: 0; left: 0;
                width: 100%; height: 100%;
                display: block;
              "
            ></iframe>
          </div>
        </div>
      </div>

    </div>
  </section>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const textRef    = ref(null)
const tvRef      = ref(null)
const textVisible = ref(false)
const tvVisible   = ref(false)

const makeObserver = (visibleRef, threshold = 0.2) => {
  let initialCheck = true
  return new IntersectionObserver(([entry]) => {
    if (initialCheck) {
      initialCheck = false
      if (entry.isIntersecting && window.scrollY < 50) return
    }
    visibleRef.value = entry.isIntersecting
  }, { threshold, rootMargin: '0px 0px -60px 0px' })
}

onMounted(() => {
  const textObs = makeObserver(textVisible, 0.3)
  const tvObs   = makeObserver(tvVisible,   0.2)
  if (textRef.value) textObs.observe(textRef.value)
  if (tvRef.value)   tvObs.observe(tvRef.value)
})
</script>

<style scoped>
/* ── TV turn-on ── */
@keyframes tv-turnon {
  0%   { transform: scaleX(0.03) scaleY(0.03); opacity: 0.9; filter: brightness(6); }
  25%  { transform: scaleX(1)    scaleY(0.03); opacity: 1;   filter: brightness(6); }
  55%  { transform: scaleX(1)    scaleY(1);                  filter: brightness(2.5); }
  75%  { filter: brightness(1.4); }
  100% { transform: scaleX(1)    scaleY(1);                  filter: brightness(1); }
}

/* ── Pulsing glow on inner video screen ── */
@keyframes screen-glow {
  0%, 100% { box-shadow: 0 0 12px 4px rgba(22, 76, 149, 0.30); }
  50%       { box-shadow: 0 0 28px 10px rgba(22, 76, 149, 0.55); }
}

.tv-frame {
  transform: scaleX(0.03) scaleY(0.03);
  opacity: 0;
  transform-origin: center center;
  box-shadow: inset 0 -20px 30px #CACBD4FF;
}

.tv-on {
  opacity: 1;
  animation: tv-turnon 1.3s ease-out forwards;
  box-shadow: inset 0 -20px 30px #CACBD4FF;
}

.video-screen {
  box-shadow: none;
}

.screen-glow {
  animation: screen-glow 3s ease-in-out 1.3s infinite;
}

/* ── Responsive ── */
@media (min-width: 769px) and (max-width: 1024px) {
  section h2 { font-size: 44px !important; }
  section p { font-size: 1.05rem !important; }
}

@media (max-width: 768px) {
  .grid.grid-cols-12 { display: flex !important; flex-direction: column !important; padding: 0 1rem !important; }
  .col-start-2.col-span-10 { width: 100% !important; }
  section h2 { font-size: 2.2rem !important; }
  .tv-frame { padding: 7px !important; }
}

/* ── Scan lines ── */
.scan-lines {
  background: repeating-linear-gradient(
    0deg,
    transparent,
    transparent 2px,
    rgba(0, 0, 0, 0.028) 2px,
    rgba(0, 0, 0, 0.028) 4px
  );
}
</style>
