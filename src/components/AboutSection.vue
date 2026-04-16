<template>
  <section id="about" class="py-20 bg-white overflow-visible" ref="sectionRef">
      <div style="height: 14px;margin-top: -5rem; margin-bottom: 10rem; background: var(--color-secondary); position: relative; z-index: 10;"></div>


    <!-- Full 12-col grid, full page width with padding -->
    <div class="grid grid-cols-12 gap-y-6 px-6" style="max-width: 1280px; margin: 0 auto;">

      <!-- HEADING: starts at col 2, spans to col 7 -->
      <div class="col-start-2 col-span-6">
        <h2 :style="{
          fontFamily: '\'Poppins\', sans-serif',
          fontWeight: 800,
          fontSize: '60px',
          color: '#164C95',
          lineHeight: 1.1,
          opacity: visible ? 1 : 0,
          transform: visible ? 'translateX(0)' : 'translateX(-70px)',
          transition: 'opacity 0.7s ease, transform 0.7s ease',
          transitionDelay: visible ? '0s' : '0s',
        }">About Me</h2>
      </div>

      <!-- ID CARD: starts at col 8, spans to col 12 — row-spans to cover heading + text + buttons -->
      <div class="col-start-8 col-span-5 row-start-1 row-span-4  flex justify-center items-start">
  <img
  src="@/assets/images/id.png"
  alt="ID Card"
  :class="visible ? 'card-drop' : 'card-hidden'"
  style="
    width: 90%;
    max-width: 500px;
    height: auto;
    object-fit: contain;
    margin-top: -170px;
    margin-left: -50px;
    z-index: 5;
    filter: drop-shadow(10px 14px 10px rgba(151, 156, 176, 0.9));
    transform-origin: top center;
  "
/>
</div>

      <!-- BODY TEXT: starts at col 2, spans to col 7 -->
      <div class="col-start-2 col-span-5">
        <p :style="{
          fontFamily: '\'Montserrat\', sans-serif',
          fontSize: '1.3rem',
          color: 'var(--color-secondary)',
          lineHeight: 1.6,
          textAlign: 'justify',
          opacity: visible ? 1 : 0,
          transform: visible ? 'translateX(0)' : 'translateX(-70px)',
          transition: 'opacity 0.7s ease, transform 0.7s ease',
          transitionDelay: visible ? '0.3s' : '0s',
        }">
          &nbsp;&nbsp;I am a <strong style="color:var(--color-secondary);">Multimedia Design student</strong> at
          SEA with a passion for bridging the gap between creative visuals and technical
          code. Originally from <strong style="color:var(--color-secondary);">Moldova</strong> and now
          based in <strong style="color:var(--color-secondary);">Denmark</strong>, I specialize in crafting
          digital experiences through clear communication and storytelling. My goal
          is to transition into <strong style="color:var(--color-secondary);">Web Development</strong>,
          combining my eye for design with a love for building responsive, functional web
          interfaces.
        </p>
      </div>

      <div class="col-start-4 col-span-4 flex items-center" :style="{
        gap: '0rem',
        marginTop: '2rem',
        opacity: visible ? 1 : 0,
        transform: visible ? 'translateX(0)' : 'translateX(-70px)',
        transition: 'opacity 0.7s ease, transform 0.7s ease',
        transitionDelay: visible ? '0.6s' : '0s',
      }">

  <!-- CV button -->
  <div class="btn-wrap">
  <a
    href="/cv.pdf"
    target="_blank"
    rel="noopener noreferrer"
    class="dl-btn transition-all hover:opacity-80"
    style="font-size:1.5rem;gap:0.5rem;"
  >
    <span v-html="downloadIcon" class="dl-icon" style="margin-left:23px;"></span>
    <span style="display:flex;align-items:center;line-height:1;margin-top:0.3rem;">CV</span>
  </a>
  </div>

  <!-- Portfolio PDF button -->
  <div class="btn-wrap">
  <a
    href="/portfolio.pdf"
    target="_blank"
    rel="noopener noreferrer"
    class="dl-btn transition-all hover:opacity-80"
    style="font-size:0.85rem;gap:0.6rem;line-height:20px;margin-left:-10px;"
  >
    <span v-html="downloadIcon" class="dl-icon" style="margin-left:20px;"></span>
    <span style="display:flex;flex-direction:column;text-align:left;line-height:1.4;width:auto;margin-top:0.4rem;">
      <span>Portfolio</span>
      <span>PDF</span>
    </span>
  </a>
  </div>
</div>
  


     <!-- LANGUAGE CARDS: col 2 to col 11 (col-start-2, col-span-10) -->
<div class="col-start-2 col-span-10" style="margin-top: 3rem;" ref="cardsRef">
  <div class="grid grid-cols-4 gap-4">
    <div
      v-for="(lang, cardIndex) in languages"
      :key="lang.name"
      class="rounded-2xl p-6"
      :style="{
        filter: 'drop-shadow(0px 3px 2px rgba(22, 76, 149, 0.6))',
        border: '0.5px solid #b8d4ef',
        background: 'white',
        boxShadow: '0 4px 16px rgba(100, 160, 220, 0.15)',
        opacity: cardsVisible ? 1 : 0,
        transform: cardsVisible ? 'translateY(0)' : 'translateY(30px)',
        transition: cardsVisible ? 'opacity 0.6s ease, transform 0.6s ease' : 'none',
        transitionDelay: cardsVisible ? `${cardIndex * 0.2}s` : '0s',
      }"
    >
      <!-- Language name -->
      <span style="
        display: block;
        font-family: 'Poppins', sans-serif;
        font-weight: 800;
        font-size: 2rem;
        color: #164C95;
        line-height: 1.1;
        margin: 0;
      ">{{ lang.name }}:</span>

      <!-- Level label -->
      <span style="
        display: block;
        font-family: 'Montserrat Alternates', sans-serif;
        font-weight: 400;
        font-size: 0.85rem;
        color: #6D9FE7;
        margin: 0;
        line-height: 1.2;
      ">{{ lang.level_label }}</span>
    </div>
  </div>
</div>

    </div>
  </section>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const sectionRef = ref(null)
const cardsRef   = ref(null)
const visible      = ref(false)
const cardsVisible = ref(false)

const makeObserver = (visibleRef, threshold) => {
  let initial = true
  return new IntersectionObserver(([entry]) => {
    if (initial) { initial = false; if (entry.isIntersecting && window.scrollY < 50) return }
    visibleRef.value = entry.isIntersecting
  }, { threshold, rootMargin: '0px 0px -60px 0px' })
}

onMounted(() => {
  if (sectionRef.value) makeObserver(visible,      0.1).observe(sectionRef.value)
  if (cardsRef.value)   makeObserver(cardsVisible, 0.2).observe(cardsRef.value)
})

const downloadIcon = `<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1920 1408" style="width:100%;height:100%;"><path fill="#164C95" d="M1280 800q0-14-9-23t-23-9h-224V416q0-13-9.5-22.5T992 384H800q-13 0-22.5 9.5T768 416v352H544q-13 0-22.5 9.5T512 800q0 14 9 23l352 352q9 9 23 9t23-9l351-351q10-12 10-24m640 224q0 159-112.5 271.5T1536 1408H448q-185 0-316.5-131.5T0 960q0-130 70-240t188-165q-2-30-2-43q0-212 150-362T768 0q156 0 285.5 87T1242 318q71-62 166-62q106 0 181 75t75 181q0 76-41 138q130 31 213.5 135.5T1920 1024"/></svg>`

const languages = [
  { name: 'ENGLISH',  level_label: 'ADVANCED' },
  { name: 'ROMANIAN', level_label: 'ADVANCED' },
  { name: 'RUSSIAN',  level_label: 'INTERMEDIATE' },
  { name: 'GERMAN',   level_label: 'BASIC' },
]
</script>

<style scoped>
/* ID card drop + pendulum swing */
@keyframes card-drop-swing {
  0%   { opacity: 0; transform: translateY(-300px) rotate(-4deg); }
  40%  { opacity: 1; transform: translateY(0)      rotate(0deg);  }
  52%  { transform: translateY(0) rotate(9deg);   }
  63%  { transform: translateY(0) rotate(-6deg);  }
  72%  { transform: translateY(0) rotate(4deg);   }
  80%  { transform: translateY(0) rotate(-2.5deg);}
  87%  { transform: translateY(0) rotate(1.5deg); }
  93%  { transform: translateY(0) rotate(-0.8deg);}
  97%  { transform: translateY(0) rotate(0.3deg); }
  100% { transform: translateY(0) rotate(0deg);   }
}

.card-hidden {
  opacity: 0;
  transform: translateY(-300px);
}

.card-drop {
  animation: card-drop-swing 3s cubic-bezier(0.33, 1, 0.68, 1) forwards;
}

@keyframes levitate {
  0%, 100% { transform: translateY(0); }
  50%       { transform: translateY(-10px); }
}

.levitate {
  animation: levitate 3.5s ease-in-out infinite;
}

.btn-wrap {
  transition: transform 0.3s ease;
}

.btn-wrap:hover {
  transform: scale(1.08);
}

.dl-btn {
  background-image: url(/button.png);
  background-size: 100% 100%;
  background-repeat: no-repeat;
  background-color: transparent;
  font-family: var(--font-display);
  font-weight: 700;
  color: #ffffff;
  border: none;
  cursor: pointer;
  width: 160px;
  height: 45px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  line-height: 1;
  padding: 0;
  text-shadow: 0px 1.5px 1px rgba(22, 76, 149, 0.5);
  text-decoration: none;
}

.dl-icon {
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  filter: brightness(0) invert(1) drop-shadow(0px 2px 1px rgba(22, 76, 149, 0.5));
  flex-shrink: 0;
}

/* ── Responsive ── */
@media (min-width: 769px) and (max-width: 1024px) {
  /* Shift only the text/button column items up — ID card (right col) is unaffected */
  .col-start-2.col-span-6 { margin-top: -5rem !important; }
  .col-start-2.col-span-5 { margin-top: -3.5rem !important; }
  .col-start-2.col-span-6 h2 { font-size: 44px !important; }
  .col-start-2.col-span-5 p { font-size: 1.05rem !important; }
  /* ID card size/position (user-tuned) */
  .col-start-8.col-span-5 img { max-width: 450px !important; margin-top: -165px !important; }
  .col-start-2.col-span-10 .rounded-2xl > span:first-child { font-size: 1.5rem !important; }
  .col-start-2.col-span-10 .rounded-2xl > span:last-child  { font-size: 0.75rem !important; }
  /* Language cards: 4 → 2 columns on tablet */
  .grid.grid-cols-4 { grid-template-columns: 1fr 1fr !important; }
  /* Buttons row: shift left to align with body text, and pull up to match */
  .col-start-4.col-span-4 {
    grid-column: 2 / span 6 !important;
    justify-content: flex-start !important;
    margin-left: 0 !important;
    margin-top: 1rem !important;
  }
}

@media (max-width: 768px) {
  /* Reset 12-col grid to single column */
  .grid.grid-cols-12 {
    display: flex !important;
    flex-direction: column !important;
    padding: 0 1rem !important;
  }

  /* ID card: full width, centered, pull up toward blue line */
  .col-start-8.col-span-5 {
    order: -1;
    width: 100% !important;
    margin: 0 auto !important;
    justify-content: center !important;
  }
  .col-start-8.col-span-5 img {
    margin-top: -165px !important;
    margin-left: 0 !important;
    max-width: 280px !important;
    width: 70% !important;
  }

  /* Heading */
  .col-start-2.col-span-6 h2 {
    font-size: 40px !important;
    text-align: center;
  }

  /* Body text */
  .col-start-2.col-span-5 p {
    font-size: 1rem !important;
    text-align: justify;
  }

  /* Buttons row: center, nudge slightly left */
  .col-start-4.col-span-4 {
    justify-content: center !important;
    width: 100% !important;
    margin-left: -8px;
  }

  /* Language cards: 2 columns, smaller text */
  .col-start-2.col-span-10 {
    width: 100% !important;
    padding: 0 !important;
  }
  .grid.grid-cols-4 {
    grid-template-columns: 1fr 1fr !important;
  }
  .col-start-2.col-span-10 .rounded-2xl { padding: 0.6rem !important; }
  .col-start-2.col-span-10 .rounded-2xl > span:first-child { font-size: 1rem !important; }
  .col-start-2.col-span-10 .rounded-2xl > span:last-child  { font-size: 0.6rem !important; }
}
</style>