<template>
<section id="contact" class="relative overflow-hidden" style="min-height: 100vh; margin-top: 5rem; display: flex; flex-direction: column;">

  <!-- Background -->
  <img
    src="@/assets/images/fot.jpg"
    alt="Sky background"
    class="contact-bg"
    style="position:absolute;top:0;left:0;width:100%;height:100%;object-fit:cover;object-position:center;min-height:100%;"
  />

  <!-- Main content -->
  <div class="relative z-10 grid grid-cols-12 px-6 contact-main-grid" style="max-width:1280px;margin:0 auto;padding-top:20rem;padding-bottom:6rem;">

    <!-- Heading -->
    <div class="col-start-2 col-span-6 mb-8" ref="headingRef">
      <h2 :style="{
        fontFamily: 'var(--font-display)',
        fontWeight: 700,
        fontSize: '5rem',
        color: '#164C95',
        lineHeight: 1.1,
        marginLeft: '-2rem',
        marginBottom: '3rem',
        opacity: headingVisible ? 1 : 0,
        transform: headingVisible ? 'translateX(0)' : 'translateX(-70px)',
        transition: headingVisible ? 'opacity 0.7s ease, transform 0.7s ease' : 'none',
      }">Let's Connect!</h2>
    </div>

    <!-- Form + socials -->
    <div class="col-start-2 col-span-5" ref="contentRef">
      <div style="display:inline-flex;flex-direction:column;width:100%;margin-left:-2rem;">

        <!-- Email card -->
        <div
          class="contact-card rounded-3xl p-4"
          :style="{
            opacity: contentVisible ? 1 : 0,
            transform: contentVisible ? 'translateY(0)' : 'translateY(30px)',
            transition: contentVisible ? 'opacity 0.6s ease, transform 0.6s ease' : 'none',
          }"
        >
          <!-- Email header -->
          <div class="flex items-center gap-3 mb-3">
            <span v-html="emailIcon" style="width:60px;height:60px;display:flex;flex-shrink:0;"></span>
            <p style="font-family:var(--font-display);font-style:italic;font-weight:700;font-size:20px;color:#164C95;letter-spacing:0.05em;">SEND ME AN EMAIL!</p>
          </div>

          <!-- Fields -->
          <div class="flex flex-col gap-1">

            <div v-for="field in fields" :key="field.key">
              <label style="font-family:var(--font-body);font-size:0.75rem;color:#6D9FE7;">{{ field.label }}</label>
              <textarea
                v-if="field.type === 'textarea'"
                v-model="form[field.key]"
                rows="4"
                class="form-input"
                style="resize:none;"
                @focus="focusedField = field.key" @blur="focusedField = null"
              ></textarea>
              <input
                v-else
                v-model="form[field.key]"
                :type="field.type"
                class="form-input"
                @focus="focusedField = field.key" @blur="focusedField = null"
              />
              <div class="field-underline" :class="{ focused: focusedField === field.key }" />
            </div>

            <!-- Send button -->
            <button
              @click="sendEmail"
              :disabled="sending"
              class="send-btn"
              style="
                align-self:flex-start;
                background:#164C95;
                color:white;
                font-family:var(--font-display);
                font-weight:600;
                font-size:0.8rem;
                letter-spacing:0.05em;
                border:none;
                border-radius:999px;
                padding:0.5rem 1.5rem;
                cursor:pointer;
                margin-top:0.5rem;
                transition: transform 0.2s ease, box-shadow 0.2s ease, opacity 0.2s;
              "
            >{{ sending ? 'Sending...' : sent ? '✓ Sent!' : 'SEND' }}</button>
          </div>
        </div>

        <div style="height:1.5rem;"></div>

        <!-- Social links -->
        <div
          class="contact-card flex items-stretch rounded-full overflow-hidden"
          :style="{
            opacity: contentVisible ? 1 : 0,
            transform: contentVisible ? 'translateY(0)' : 'translateY(30px)',
            transition: contentVisible ? 'opacity 0.6s ease, transform 0.6s ease' : 'none',
            transitionDelay: contentVisible ? '0.3s' : '0s',
          }"
        >
          <a
            v-for="(social, index) in socials"
            :key="social.label"
            :href="social.href || '#'"
            target="_blank"
            rel="noopener noreferrer"
            class="social-link flex items-center gap-2 hover:bg-blue-50 transition-colors"
            :style="social.border ? 'border-right:1.5px solid #ffffff;padding:0.625rem 1.5rem;align-self:stretch;' : 'padding:0.625rem 1.5rem;flex:1;align-self:stretch;'"
          >
            <span class="social-icon" v-html="social.icon" style="width:37px;height:37px;display:flex;flex-shrink:0;"></span>
            <div>
              <p style="font-family:var(--font-body);font-size:0.6rem;color:#6D9FE7;line-height:1;">{{ social.sub }}</p>
              <p style="font-family:var(--font-body);font-weight:700;font-size:0.7rem;color:#5082C0;line-height:1.2;">{{ social.label }}</p>
            </div>
          </a>
        </div>

      </div>
    </div>
  </div>

  <!-- Footer -->
  <div class="relative z-10 w-full" style="background:var(--color-primary);margin-top:auto;" ref="footerRef">
    <!-- Scroll progress bar -->
    <div style="position:absolute;top:0;left:0;height:3px;background:rgba(255,255,255,0.9);border-radius:0 2px 2px 0;transition:width 0.1s linear;" :style="{ width: scrollProgress + '%' }" />

    <div class="grid grid-cols-12 items-center px-10 py-5">
      <!-- Logo + name with shine -->
      <div class="col-span-4 flex items-center gap-3">
        <div class="logo-shine-wrap" style="display:flex;align-items:center;gap:0.75rem;position:relative;overflow:hidden;border-radius:4px;cursor:default;">
          <img :src="whiteLogo" alt="MH Logo" style="height:58px;width:auto;object-fit:contain;position:relative;z-index:1;" />
          <div class="flex flex-col" style="gap:0;line-height:1.1;margin-top:1.8rem;position:relative;z-index:1;">
            <p style="font-family:var(--font-display);font-weight:700;font-style:italic;color:white;font-size:0.95rem;margin:0;padding:0;"><span style="font-size:1.2em;">M</span>ihaela <span style="font-size:1.2em;">H</span>ioara</p>
            <p style="font-family:var(--font-body);font-size:0.7rem;color:rgba(255,255,255,0.7);margin:0;padding:0;">Multimedia Designer</p>
          </div>
          <div class="logo-shine" />
        </div>
      </div>

      <!-- Back to top -->
      <div class="col-span-4 flex flex-col items-center gap-1">
        <a
          href="#home"
          @click.prevent="handleScrollToTop"
          class="back-to-top flex flex-col items-center gap-1"
          :class="{ spinning: arrowSpinning }"
          style="margin-left:1rem;cursor:pointer;"
        >
          <svg xmlns="http://www.w3.org/2000/svg" width="36" height="36" viewBox="0 0 16 16" class="arrow-icon">
            <path fill="#fff" d="m2.931 10.843l4.685-4.611a.546.546 0 0 1 .768 0l4.685 4.61a.55.55 0 0 0 .771 0a.53.53 0 0 0 0-.759l-4.684-4.61a1.65 1.65 0 0 0-2.312 0l-4.684 4.61a.53.53 0 0 0 0 .76a.55.55 0 0 0 .771 0" stroke-width="0.6" stroke="#fff"/>
          </svg>
          <p style="font-family:var(--font-body);font-weight:600;font-size:0.7rem;color:white;letter-spacing:0.1em;">BACK TO TOP</p>
        </a>
      </div>

      <!-- Copyright — fades in -->
      <div class="col-span-4 flex justify-end" style="margin-top:1.8rem;">
        <p
          style="font-family:var(--font-body);font-size:0.75rem;color:rgba(255,255,255,0.8);transition:opacity 1s ease 0.4s;"
          :style="{ opacity: footerVisible ? 1 : 0 }"
        >© 2026 All Rights Reserved.</p>
      </div>
    </div>
  </div>

</section>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import whiteLogo from '@/assets/images/white.png'

const scrollToTop = () => document.getElementById('home')?.scrollIntoView({ behavior: 'smooth' })

// Scroll progress + footer visibility
const scrollProgress = ref(0)
const footerRef      = ref(null)
const footerVisible  = ref(false)
const arrowSpinning  = ref(false)

const updateScroll = () => {
  const total = document.documentElement.scrollHeight - window.innerHeight
  scrollProgress.value = total > 0 ? Math.min((window.scrollY / total) * 100, 100) : 0
}

const handleScrollToTop = () => {
  arrowSpinning.value = true
  scrollToTop()
  setTimeout(() => { arrowSpinning.value = false }, 600)
}

const form     = ref({ name: '', email: '', message: '' })
const sending  = ref(false)
const sent     = ref(false)
const focusedField = ref(null)

const headingRef    = ref(null)
const contentRef    = ref(null)
const headingVisible = ref(false)
const contentVisible = ref(false)

const makeObserver = (visibleRef, threshold = 0.2) => {
  let initial = true
  return new IntersectionObserver(([entry]) => {
    if (initial) { initial = false; if (entry.isIntersecting && window.scrollY < 50) return }
    visibleRef.value = entry.isIntersecting
  }, { threshold, rootMargin: '0px 0px -60px 0px' })
}

onMounted(() => {
  if (headingRef.value) makeObserver(headingVisible, 0.3).observe(headingRef.value)
  if (contentRef.value) makeObserver(contentVisible, 0.2).observe(contentRef.value)

  // Footer visibility for copyright fade
  if (footerRef.value) {
    new IntersectionObserver(([e]) => { footerVisible.value = e.isIntersecting }, { threshold: 0.3 })
      .observe(footerRef.value)
  }

  window.addEventListener('scroll', updateScroll, { passive: true })
  updateScroll()
})

onUnmounted(() => {
  window.removeEventListener('scroll', updateScroll)
})

const sendEmail = async () => {
  if (!form.value.name || !form.value.email || !form.value.message) return
  sending.value = true
  try {
    const data = new FormData()
    data.append('name', form.value.name)
    data.append('email', form.value.email)
    data.append('message', form.value.message)
    data.append('_replyto', form.value.email)
    data.append('_subject', 'New message from portfolio')
    await fetch('https://formsubmit.co/hioaramihaela01@gmail.com', {
      method: 'POST', body: data, headers: { 'Accept': 'application/json' }
    })
    sent.value = true
    form.value = { name: '', email: '', message: '' }
    setTimeout(() => sent.value = false, 4000)
  } catch (e) { console.error(e) }
  sending.value = false
}

const fields = [
  { key: 'name',    label: 'Your Name:',    type: 'text' },
  { key: 'email',   label: 'Your Email:',   type: 'email' },
  { key: 'message', label: 'Your Message:', type: 'textarea' },
]

const emailIcon    = `<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><g fill="none" stroke="#164C95" stroke-linecap="round" stroke-linejoin="round" stroke-width="2"><path d="M4 5h16c0.55 0 1 0.45 1 1v12c0 0.55 -0.45 1 -1 1h-16c-0.55 0 -1 -0.45 -1 -1v-12c0 -0.55 0.45 -1 1 -1Z"/><path d="M3 6.5l9 5.5l9 -5.5"/></g></svg>`
const linkedinIcon = `<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" style="width:100%;height:100%;"><path fill="#164C95" d="M19 3a2 2 0 0 1 2 2v14a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2zm-.5 15.5v-5.3a3.26 3.26 0 0 0-3.26-3.26c-.85 0-1.84.52-2.32 1.3v-1.11h-2.79v8.37h2.79v-4.93c0-.77.62-1.4 1.39-1.4a1.4 1.4 0 0 1 1.4 1.4v4.93zM6.88 8.56a1.68 1.68 0 0 0 1.68-1.68c0-.93-.75-1.69-1.68-1.69a1.69 1.69 0 0 0-1.69 1.69c0 .93.76 1.68 1.69 1.68m1.39 9.94v-8.37H5.5v8.37z"/></svg>`
const whatsappIcon = `<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" style="width:100%;height:100%;"><path fill="#164C95" fill-rule="evenodd" d="M1 12C1 5.925 5.925 1 12 1s11 4.925 11 11s-4.925 11-11 11c-1.936 0-3.757-.5-5.338-1.38L1.4 22.6l.98-5.262A10.95 10.95 0 0 1 1 12m6.147-5.353c.654-.655 1.527-.713 1.998-.69c.435.02.776.272.984.564l.952 1.334a1.5 1.5 0 0 1-.16 1.932l-.674.673c.223.544.67 1.42 1.272 2.021s1.477 1.05 2.02 1.273l.675-.674a1.5 1.5 0 0 1 1.931-.16l1.335.951c.291.209.544.55.564.984c.022.471-.036 1.344-.69 1.999c-1.067 1.066-2.741 1.077-4.264.659c-1.55-.426-3.13-1.34-4.197-2.406c-1.065-1.066-1.98-2.646-2.406-4.197c-.418-1.523-.407-3.197.66-4.263" clip-rule="evenodd"/></svg>`
const githubIcon   = `<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" style="width:100%;height:100%;"><path fill="#164C95" d="M12 2A10 10 0 0 0 2 12c0 4.42 2.87 8.17 6.84 9.5c.5.08.66-.23.66-.5v-1.69c-2.77.6-3.36-1.34-3.36-1.34c-.46-1.16-1.11-1.47-1.11-1.47c-.91-.62.07-.6.07-.6c1 .07 1.53 1.03 1.53 1.03c.87 1.52 2.34 1.07 2.91.83c.09-.65.35-1.09.63-1.34c-2.22-.25-4.55-1.11-4.55-4.92c0-1.11.38-2 1.03-2.71c-.1-.25-.45-1.29.1-2.64c0 0 .84-.27 2.75 1.02c.79-.22 1.65-.33 2.5-.33s1.71.11 2.5.33c1.91-1.29 2.75-1.02 2.75-1.02c.55 1.35.2 2.39.1 2.64c.65.71 1.03 1.6 1.03 2.71c0 3.82-2.34 4.66-4.57 4.91c.36.31.69.92.69 1.85V21c0 .27.16.59.67.5C19.14 20.16 22 16.42 22 12A10 10 0 0 0 12 2"/></svg>`

const socials = [
  { label: 'LinkedIn',  sub: 'Connect on',        icon: linkedinIcon, border: true,  href: 'https://www.linkedin.com/in/mihaela-hioara' },
  { label: 'WhatsApp',  sub: 'Send a message on',  icon: whatsappIcon, border: true,  href: 'https://wa.me/4555253239' },
  { label: 'GitHub',    sub: 'Check out my',        icon: githubIcon,   border: false, href: 'https://github.com/Mihaela1909' },
]
</script>

<style scoped>
/* ── Email / social cards shared static styles ── */
.contact-card {
  filter: drop-shadow(0px 3px 2px rgba(22, 76, 149, 0.2));
  border: 1px solid #b8d4ef;
  background: rgba(255, 255, 255, 0.8);
  box-shadow: 0 3px 7px rgba(22, 76, 149, 0.5);
}

/* ── Form inputs ── */
.form-input {
  width: 100%;
  border: none;
  outline: none;
  background: transparent;
  font-family: var(--font-body);
  font-size: 0.85rem;
  color: #164C95;
  padding: 4px 0;
}

/* ── Field underline animation ── */
.field-underline {
  height: 1.5px;
  background: #b8d4ef;
  position: relative;
  margin-bottom: 6px;
}
.field-underline::after {
  content: '';
  position: absolute;
  left: 0; top: 0;
  height: 100%;
  width: 0;
  background: #164C95;
  transition: width 0.35s ease;
}
.field-underline.focused::after {
  width: 100%;
}

/* ── Send button pulse ── */
@keyframes btn-pulse {
  0%, 100% { box-shadow: 0 0 0 0 rgba(22, 76, 149, 0.0); }
  50%       { box-shadow: 0 0 10px 5px rgba(22, 76, 149, 0.2); }
}
.send-btn {
  animation: btn-pulse 2s ease-in-out infinite;
}
.send-btn:hover {
  transform: scale(1.07) !important;
  box-shadow: 0 0 18px 8px rgba(22, 76, 149, 0.35) !important;
  animation: none;
}

/* ── Back to top arrow bounce ── */
@keyframes arrow-bounce {
  0%, 100% { transform: translateY(0);   }
  50%       { transform: translateY(-6px); }
}
@keyframes arrow-spin {
  from { transform: rotate(0deg); }
  to   { transform: rotate(-360deg); }
}
.back-to-top { transition: transform 0.2s ease, filter 0.2s ease; }
.back-to-top .arrow-icon { animation: arrow-bounce 1.6s ease-in-out infinite; }
.back-to-top:hover { transform: scale(1.2); filter: brightness(1.4); }
.back-to-top:hover .arrow-icon { animation: none; }
.back-to-top.spinning .arrow-icon { animation: arrow-spin 0.5s ease forwards; }

/* ── Logo shine sweep ── */
@keyframes logo-shine {
  0%   { left: -80%; }
  100% { left: 130%; }
}
.logo-shine {
  position: absolute;
  top: 0; left: -80%;
  width: 50%; height: 100%;
  background: linear-gradient(120deg, transparent 0%, rgba(255,255,255,0.3) 50%, transparent 100%);
  pointer-events: none;
  opacity: 0;
}
.logo-shine-wrap:hover .logo-shine {
  opacity: 1;
  animation: logo-shine 0.7s ease forwards;
}

/* ── Social icon bounce on hover ── */
.social-link {
  transition: background 0.2s ease;
}
.social-link:hover .social-icon {
  animation: icon-bounce 0.4s cubic-bezier(0.34, 1.56, 0.64, 1) forwards;
}
@keyframes icon-bounce {
  0%   { transform: rotate(0deg)   scale(1);    }
  40%  { transform: rotate(-8deg)  scale(1.15); }
  70%  { transform: rotate(5deg)   scale(1.1);  }
  100% { transform: rotate(0deg)   scale(1);    }
}

/* ── Responsive ── */
@media (min-width: 769px) and (max-width: 1024px) {
  .col-start-2.col-span-6 h2 { font-size: 3.5rem !important; margin-left: -1rem !important; }
  /* Push all content lower so it clears the sky image */
  .contact-main-grid { padding-top: 20rem !important; }
  .grid.grid-cols-12.items-center { padding: 1rem 2rem !important; }
  /* Form + socials span wider on tablet */
  .col-start-2.col-span-5 { grid-column: 2 / span 7 !important; }
  /* Social links: compact padding on tablet */
  .social-link { padding: 0.5rem 1rem !important; }
}

@media (max-width: 768px) {
  .grid.grid-cols-12 { display: flex !important; flex-direction: column !important; padding: 0 1rem !important; }
  .col-start-2.col-span-6 { width: 100% !important; }
  .col-start-2.col-span-5 { width: 100% !important; }
  .col-start-2.col-span-6 h2 { font-size: 2.6rem !important;margin-top: 2rem !important; margin-left: 0 !important; margin-bottom: 1rem !important; }

  /* Image pulled up inside section, clipped at section boundary */
  .contact-bg { top: -230px !important; margin-top: 0 !important; height: calc(80% + 270px) !important; min-height: unset !important; object-position: top center !important; }
  .contact-main-grid { padding-top: 3rem !important; padding-bottom: 4rem !important; }

  /* Form card full width */
  .col-start-2.col-span-5 > div {
    margin-left: 0 !important;
    width: 100% !important;
  }

  /* Social links: single row, compact */
  .flex.items-stretch.rounded-full { flex-wrap: nowrap !important; border-radius: 999px !important; }
  .social-link { flex: 1 1 0 !important; padding: 0.5rem 0.5rem !important; border-right: 1.5px solid #e0edf8 !important; border-bottom: none !important; min-width: 0; }
  .social-link:last-child { border-right: none !important; }
  .social-link .social-icon { width: 24px !important; height: 24px !important; }
  .social-link > div > p { font-size: 0.5rem !important; }

  /* More space between contact content and footer */
  .col-start-2.col-span-5 { margin-bottom: 3rem; }

  /* Footer: stack on mobile, reorder */
  .grid.grid-cols-12.items-center { display: flex !important; flex-direction: column !important; align-items: center !important; gap: 1rem; padding: 1rem !important; }
  .col-span-4 { width: 100% !important; display: flex; justify-content: center; margin-top: 0 !important; }
  .logo-shine-wrap { justify-content: center; }

  /* Footer item order: back-to-top first, logo second, copyright third */
  .col-span-4.flex.items-center.gap-3 { order: 2; }
  .col-span-4.flex.flex-col.items-center.gap-1 { order: 1; }
  .col-span-4.flex.justify-end { order: 3; }
}
</style>
