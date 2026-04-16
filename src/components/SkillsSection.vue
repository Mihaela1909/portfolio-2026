<template>
  <section id="skills" class="py-20 relative" ref="sectionRef" :style="{
    backgroundImage: `url(${clBg})`,
    backgroundSize: 'cover',
    backgroundPosition: 'bottom',
    backgroundRepeat: 'no-repeat',
    marginTop: '10rem',
  }">

    <div class="grid grid-cols-12 px-6 relative z-10" style="max-width: 1280px; margin: 0 auto;">

      <!-- Heading: fades in from top -->
      <div class="col-span-12 text-center mb-10" ref="headingRef">
        <h2 :style="{
          fontFamily: 'var(--font-display)',
          fontWeight: 700,
          fontSize: '4rem',
          color: 'var(--color-primary)',
          marginTop: '4rem',
          marginBottom: '5rem',
          opacity: headingVisible ? 1 : 0,
          transform: headingVisible ? 'translateY(0)' : 'translateY(-50px)',
          transition: headingVisible ? 'opacity 0.7s ease, transform 0.7s ease' : 'none',
        }">SKILLS</h2>
      </div>

      <!-- Cards: entrance wrapper + tilt card -->
      <div class="col-start-2 col-span-10">
        <div class="grid grid-cols-2 gap-6" style="perspective: 1000px;">

          <!-- Entrance wrapper (handles slide-in) -->
          <div
            v-for="(skill, index) in skills"
            :key="skill.name"
            :style="{
              opacity: visible ? 1 : 0,
              transform: visible
                ? 'translateX(0)'
                : (index % 2 === 0 ? 'translateX(-80px)' : 'translateX(80px)'),
              transition: 'opacity 0.6s ease, transform 0.6s ease',
              transitionDelay: `${Math.floor(index / 2) * 0.8}s`,
            }"
          >
            <!-- Tilt card -->
            <div
              class="rounded-2xl flex items-center gap-5"
              :style="{
                filter: 'drop-shadow(0px 3px 2px rgba(22, 76, 149, 0.2))',
                border: '1px solid #b8d4ef',
                background: 'rgba(255, 255, 255, 0.8)',
                boxShadow: hoveredCard === index
                  ? '0 12px 28px rgba(22, 76, 149, 0.35)'
                  : '0 3px 7px rgba(22, 76, 149, 0.5)',
                padding: '30px 30px',
                transform: tiltStates[index]
                  ? `rotateX(${tiltStates[index].x}deg) rotateY(${tiltStates[index].y}deg)`
                  : 'rotateX(0deg) rotateY(0deg)',
                transition: 'transform 0.12s ease, box-shadow 0.3s ease',
                transformStyle: 'preserve-3d',
                willChange: 'transform',
              }"
              @mouseenter="hoveredCard = index"
              @mousemove="handleTilt($event, index)"
              @mouseleave="handleTiltReset(index)"
            >
              <!-- Icon — scales on hover -->
              <div
                class="flex-shrink-0 flex items-center justify-center rounded-2xl"
                :style="{
                  width: '100px',
                  height: '100px',
                  transform: hoveredCard === index ? 'scale(1.2)' : 'scale(1)',
                  transition: 'transform 0.4s cubic-bezier(0.34, 1.56, 0.64, 1)',
                }"
                v-html="skill.icon"
              ></div>

              <!-- Text -->
              <div>
                <p style="
                  font-family: var(--font-display);
                  font-weight: 700;
                  font-size: 2.5rem;
                  color: var(--color-primary);
                  margin-top: 10px;
                ">{{ skill.name }}</p>
                <p style="
                  font-family: var(--font-body);
                  font-weight: 400;
                  font-size: 1.45rem;
                  color: var(--color-secondary);
                  line-height: 1.4;
                  margin-bottom: 10px;
                ">{{ skill.desc }}</p>
              </div>
            </div>
          </div>

        </div>
      </div>

      <!-- Bottom padding so image shows below cards -->
      <div class="col-span-12" style="height: 6rem;"></div>

    </div>
  </section>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import clBg from '@/assets/images/cl.jpg'

const sectionRef  = ref(null)
const headingRef  = ref(null)
const visible        = ref(false)
const headingVisible = ref(false)
const hoveredCard    = ref(null)
const tiltStates     = ref(Array(6).fill(null).map(() => ({ x: 0, y: 0 })))

const handleTilt = (e, index) => {
  const card = e.currentTarget
  const rect = card.getBoundingClientRect()
  const cx = rect.width  / 2
  const cy = rect.height / 2
  const mx = e.clientX - rect.left
  const my = e.clientY - rect.top
  tiltStates.value[index] = {
    x: ((my - cy) / cy) * -8,
    y: ((mx - cx) / cx) *  8,
  }
}

const handleTiltReset = (index) => {
  tiltStates.value[index] = { x: 0, y: 0 }
  hoveredCard.value = null
}

const makeObserver = (visibleRef, threshold, rootMargin = '0px 0px -80px 0px') =>
  new IntersectionObserver(
    (() => {
      let initial = true
      return ([entry]) => {
        if (initial) { initial = false; if (entry.isIntersecting && window.scrollY < 50) return }
        visibleRef.value = entry.isIntersecting
      }
    })(),
    { threshold, rootMargin }
  )

onMounted(() => {
  const headingObs = makeObserver(headingVisible, 0.5)
  const cardsObs   = makeObserver(visible, 0.15)
  if (headingRef.value)  headingObs.observe(headingRef.value)
  if (sectionRef.value)  cardsObs.observe(sectionRef.value)
})

const skills = [
  {
    name: 'Figma',
    desc: 'Web Design, and UI/UX prototyping',
    icon: `<svg xmlns="http://www.w3.org/2000/svg" style="width:100%;height:100%;" viewBox="0 0 24 24"><path fill="#164C95" d="M11.667 2H8.333a3.333 3.333 0 1 0 0 6.667h3.334z" opacity="0.6"/><path fill="#164C95" d="M11.667 8.667H8.333a3.333 3.333 0 0 0 0 6.666h3.334z" opacity="0.4"/><path fill="#164C95" d="M18.333 12a3.333 3.333 0 1 1-6.667 0a3.333 3.333 0 0 1 6.667 0"/><path fill="#164C95" d="M8.333 15.334h3.334v3.333a3.333 3.333 0 1 1-3.334-3.334" opacity="0.2"/><path fill="#164C95" d="M11.666 2h3.333a3.333 3.333 0 1 1 0 6.667h-3.333z" opacity="0.8"/></svg>`
  },
  {
    name: 'HTML',
    desc: 'Semantic web structuring & Content Architecture.',
    icon: `<svg xmlns="http://www.w3.org/2000/svg" style="width:100%;height:100%;" viewBox="0 0 256 256"><g fill="none"><rect width="256" height="256" fill="#164C95" rx="60"/><path fill="#fff" d="m48 38l8.61 96.593h110.71l-3.715 41.43l-35.646 9.638l-35.579-9.624l-2.379-26.602H57.94l4.585 51.281l65.427 18.172l65.51-18.172l8.783-98.061H85.824l-2.923-32.71h122.238L208 38z"/><path fill="#9ab6dc" d="M128 38H48l8.61 96.593H128v-31.938H85.824l-2.923-32.71H128zm0 147.647l-.041.014l-35.579-9.624l-2.379-26.602H57.94l4.585 51.281l65.427 18.172l.049-.014z"/></g></svg>`
  },
  {
    name: 'JavaScript',
    desc: 'Dynamic front-end logic & Interactive Features.',
    icon: `<svg xmlns="http://www.w3.org/2000/svg" style="width:100%;height:100%;" viewBox="0 0 448 512"><path fill="#164C95" d="M400 32H48C21.5 32 0 53.5 0 80v352c0 26.5 21.5 48 48 48h352c26.5 0 48-21.5 48-48V80c0-26.5-21.5-48-48-48M243.8 381.4c0 43.6-25.6 63.5-62.9 63.5c-33.7 0-53.2-17.4-63.2-38.5l34.3-20.7c6.6 11.7 12.6 21.6 27.1 21.6c13.8 0 22.6-5.4 22.6-26.5V237.7h42.1zm99.6 63.5c-39.1 0-64.4-18.6-76.7-43l34.3-19.8c9 14.7 20.8 25.6 41.5 25.6c17.4 0 28.6-8.7 28.6-20.8c0-14.4-11.4-19.5-30.7-28l-10.5-4.5c-30.4-12.9-50.5-29.2-50.5-63.5c0-31.6 24.1-55.6 61.6-55.6c26.8 0 46 9.3 59.8 33.7L368 290c-7.2-12.9-15-18-27.1-18c-12.3 0-20.1 7.8-20.1 18c0 12.6 7.8 17.7 25.9 25.6l10.5 4.5c35.8 15.3 55.9 31 55.9 66.2c0 37.8-29.8 58.6-69.7 58.6"/></svg>`
  },
  {
    name: 'CSS',
    desc: 'Responsive Styling, Layouts, & Visual Polish.',
    icon: `<svg xmlns="http://www.w3.org/2000/svg" style="width:100%;height:100%;" viewBox="0 0 128 128"><path fill="#164C95" d="M0 107.52C0 118.831 9.168 128 20.48 128h87.04c11.312 0 20.48-9.168 20.48-20.48V20.48C128 9.169 118.832 0 107.52 0H0Zm45.836 10.242c-8.219-.008-13.293-4.64-13.195-13.16V83.05q0-6.481 3.793-9.852c4.543-4.379 15.082-4.644 19.53.067c2.743 2.418 3.778 7.355 3.532 11.964H50.06c.074-1.808-.024-4.554-1.09-5.586c-1.383-1.87-5.035-1.652-6.004.297c-.594 1.059-.89 2.621-.89 4.696v18.71q0 5.884 4.09 5.95q1.914-.001 2.905-1.39c.918-1.098 1.063-3.528.989-5.29h9.437c.645 8.969-4.652 15.254-13.66 15.145Zm29.957 0c-9.11.125-13.184-6.36-12.934-15.145h8.91c-.25 3.832 1.067 7.32 4.223 7.078q2.108 0 2.969-1.324c1.086-1.613 1.293-6.265-.266-8.066c-1.086-1.735-4.996-3.266-7.058-4.297q-4.423-2.115-6.367-5.027c-2.93-4.305-2.657-13.754 1.449-17.586c3.992-4.727 14.414-4.942 18.41-.098c2.465 2.496 3.539 7.41 3.332 11.934h-8.578c.074-1.86-.102-4.86-.824-5.95q-.762-1.39-2.871-1.39q-3.762.001-3.762 4.496c.027 3.18 1.27 4.488 4.16 5.816c3.742 1.453 8.5 3.938 10.226 6.942c5.145 9.16 1.614 23.148-11.019 22.613Zm28.77 0c-9.11.125-13.184-6.36-12.934-15.145h8.91c-.25 3.832 1.067 7.32 4.223 7.078q2.108 0 2.969-1.324c1.085-1.613 1.289-6.265-.266-8.066c-1.086-1.735-4.996-3.266-7.059-4.297q-4.423-2.115-6.367-5.027c-2.93-4.305-2.656-13.754 1.45-17.586c3.992-4.727 14.413-4.942 18.41-.098c2.464 2.496 3.539 7.41 3.332 11.934h-8.579c.07-1.86-.101-4.86-.824-5.95q-.762-1.39-2.871-1.39q-3.762.001-3.762 4.496c.028 3.18 1.27 4.488 4.16 5.816c3.743 1.453 8.5 3.938 10.227 6.942c5.145 9.16 1.613 23.148-11.02 22.613Z"/></svg>`
  },
  {
    name: 'Adobe CC',
    desc: 'Visual Identity & Asset Creation',
    icon: `<svg xmlns="http://www.w3.org/2000/svg" style="width:100%;height:100%;" viewBox="0 0 20 20"><path fill="#164C95" d="M12.6 3c-1.966 0-3.74.813-5.012 2.119A6 6 0 1 0 6.4 17h6.2a7 7 0 1 0 0-14M6.4 15.728a4.7 4.7 0 0 1-3.344-1.385C2.164 13.45 1.672 12.262 1.672 11s.492-2.45 1.385-3.343S5.137 6.272 6.4 6.272s2.45.492 3.344 1.385l1.874 1.875a.7.7 0 0 1-.99.99L8.754 8.646c-1.258-1.256-3.449-1.256-4.707 0c-.629.63-.975 1.465-.975 2.354s.346 1.724.975 2.354c.785.784 1.933 1.078 2.991.884c.324.424.689.815 1.1 1.155a4.7 4.7 0 0 1-1.738.335m10.289-1.639a5.75 5.75 0 0 1-4.089 1.694a5.75 5.75 0 0 1-4.09-1.694L5.801 11.38a.7.7 0 0 1 .99-.99L9.5 13.099c.828.828 1.929 1.284 3.1 1.284s2.271-.456 3.099-1.284S16.983 11.17 16.983 10s-.456-2.271-1.284-3.099s-1.928-1.284-3.099-1.284c-.873 0-1.707.255-2.418.727a6 6 0 0 0-1.251-.779c1.035-.858 2.309-1.349 3.67-1.349c1.544 0 2.996.602 4.089 1.694s1.694 2.545 1.694 4.089s-.602 2.998-1.695 4.09"/></svg>`
  },
  {
    name: 'Clip Studio',
    desc: 'Digital Illustration and specialized 2D Concept Art',
    icon: `<svg xmlns="http://www.w3.org/2000/svg" style="width:100%;height:100%;" viewBox="0 0 48 48"><path fill="none" stroke="#164C95" stroke-linecap="round" stroke-linejoin="round" d="m26.652 42.5l-4.672-7.023l13.044-6.682c6.388-3.273 8.324-10.803 4.322-16.819h0C35.345 5.961 26.922 3.737 20.533 7.01l-10.6 5.43c-3.354 1.718-4.37 5.671-2.27 8.83h0c2.101 3.157 6.523 4.324 9.877 2.606l9.395-4.813" stroke-width="6"/></svg>`
  },
]
</script>

<style scoped>
@media (min-width: 769px) and (max-width: 1024px) {
  section h2 { font-size: 2.8rem !important; }
  .flex-shrink-0.flex.items-center.justify-center.rounded-2xl { width: 72px !important; height: 72px !important; }
  .rounded-2xl.flex.items-center { padding: 20px 22px !important; height: 100% !important; box-sizing: border-box !important; }
  .rounded-2xl.flex.items-center > div > p:first-child { font-size: 1.8rem !important; }
  .rounded-2xl.flex.items-center > div > p:last-child { font-size: 1.1rem !important; }
  /* Equal-height rows: stretch entrance wrappers to fill grid cell */
  .grid.grid-cols-2 { align-items: stretch !important; }
  .grid.grid-cols-2 > div { display: flex !important; flex-direction: column !important; }
}

@media (max-width: 768px) {
  .grid.grid-cols-12 { display: flex !important; flex-direction: column !important; padding: 0 1rem !important; }
  .col-start-2.col-span-10 { width: 100% !important; }
  .grid.grid-cols-2 { grid-template-columns: 1fr !important; }
  section h2 { font-size: 2.5rem !important; text-align: center; }
  /* Reduce card content sizes */
  .flex-shrink-0.flex.items-center.justify-center.rounded-2xl { width: 80px !important; height: 80px !important; }
  .rounded-2xl.flex.items-center { padding: 14px 18px !important; gap: 0.75rem !important; }
  .rounded-2xl.flex.items-center > div > p:first-child { font-size: 1.3rem !important; margin-top: 0 !important; }
  .rounded-2xl.flex.items-center > div > p:last-child { font-size: 0.85rem !important; margin-bottom: 0 !important; }
}
</style>
