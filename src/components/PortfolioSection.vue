<template>
  <section id="portfolio" class="py-20 bg-white">
    <div class="grid grid-cols-12 px-6" style="max-width: 1280px; margin: 0 auto;">

      <!-- Heading: slides in from left -->
      <div class="col-start-2 col-span-10 mb-12" ref="headingRef">
        <h2 :style="{
          fontFamily: 'var(--font-display)',
          fontWeight: 800,
          fontSize: '3.5rem',
          color: 'var(--color-primary)',
          lineHeight: 1,
          marginTop: '90px',
          opacity: headingVisible ? 1 : 0,
          transform: headingVisible ? 'translateX(0)' : 'translateX(-70px)',
          transition: headingVisible ? 'opacity 0.7s ease, transform 0.7s ease' : 'none',
        }">PORTFOLIO</h2>
        <p :style="{
          fontFamily: 'var(--font-body)',
          fontWeight: 400,
          fontSize: '1rem',
          color: 'var(--color-secondary)',
          marginTop: '0.2rem',
          marginBottom: '20px',
          opacity: headingVisible ? 1 : 0,
          transform: headingVisible ? 'translateX(0)' : 'translateX(-70px)',
          transition: headingVisible ? 'opacity 0.7s ease, transform 0.7s ease' : 'none',
          transitionDelay: headingVisible ? '0.25s' : '0s',
        }">Check my work out!</p>
      </div>

      <!-- 4 portfolio items -->
      <div class="col-start-2 col-span-10" ref="cardsRef">
        <div class="grid grid-cols-4 gap-10">
          <div
            v-for="(item, index) in items"
            :key="item.label"
            class="flex flex-col items-center cursor-pointer group relative card-scale"
            @click="openModal(item)"
          >
            <!-- Card wrapper: bounce entrance, then levitate -->
            <div
              :class="cardsVisible ? 'levitate' : ''"
              :style="{
                width: '115%',
                opacity: cardsVisible ? 1 : 0,
                transform: cardsVisible ? undefined : 'translateY(-60px)',
                transition: cardsVisible ? 'opacity 0.5s ease' : 'none',
                transitionDelay: `${index * 0.18}s`,
                animationDelay: `${index * 0.15}s`,
                position: 'relative',
              }"
            >
              <!-- Shine sweep overlay -->
              <div class="shine-wrap" style="position: relative; border-radius: 12px; overflow: hidden;">
                <img
                  :src="item.image"
                  :alt="item.label"
                  class="w-full h-auto"
                  style="object-fit: contain; display: block;"
                />
                <!-- Label locked inside the card image -->
                <p
                  class="card-label"
                  style="
                    position: absolute;
                    bottom: 18%;
                    left: 0; right: 0;
                    text-align: center;
                    z-index: 10;
                    font-family: var(--font-display);
                    font-weight: 800;
                    font-size: 0.85rem;
                    color: #ffffff;
                    letter-spacing: 0.12em;
                    text-shadow: 0px 1px 3px rgba(0,0,0,0.3);
                  "
                >{{ item.label }}</p>
                <div class="shine" />
              </div>
            </div>

            <!-- Shadow below the card -->
            <div
              class="shadow-pulse"
              :style="{ animationDelay: `${index * 0.15}s`, opacity: cardsVisible ? 1 : 0, transition: 'opacity 0.3s ease' }"
            />
          </div>
        </div>
      </div>

    </div>
  </section>

  <!-- Windows XP modal -->
  <PortfolioModal
    v-model="modalOpen"
    :categories="items"
    :initialCategory="activeItem.label"
  />
</template>

<script setup>
import { ref, onMounted } from 'vue'
import cameraImg  from '@/assets/images/camera.png'
import webImg     from '@/assets/images/web.png'
import illImg     from '@/assets/images/ill.png'
import brandImg   from '@/assets/images/brand.png'
import miniMeImg      from '@/assets/projects/mini me.jpg'
import march12Img     from '@/assets/projects/March_12_Infographic.jpg'
// #GreenNorgesgade — folder renamed to GreenNorgesgade on disk (# breaks Vite module resolution)
import gnPdfUrl    from '@/assets/projects/GreenNorgesgade/GreenNorgesgade_Idea_Catalogue.pdf?url'
import gnPosterImg from '@/assets/projects/GreenNorgesgade/GreenNorgesgade_Poster.jpg'
import gnSoMe1Img  from '@/assets/projects/GreenNorgesgade/SoMe_post_1.jpg'
import gnSoMe2Img  from '@/assets/projects/GreenNorgesgade/SoMe_post_2.jpg'
import gnSoMe3Img  from '@/assets/projects/GreenNorgesgade/SoMe_post_3.jpg'
import PortfolioModal from './PortfolioModal.vue'

// ── Helpers ──
const toDisplayName = (path) => {
  const raw = path.split('/').pop().replace(/\.[^.]+$/, '')
  return raw.replace(/_/g, ' ').replace(/\b\w/g, c => c.toUpperCase()) + '.jpg'
}

// ── Photography ──
const photoGlob = import.meta.glob('../assets/photography/*.jpg', { eager: true })
const photographyImages = Object.entries(photoGlob)
  .sort(([a], [b]) => a.localeCompare(b))
  .map(([path, mod]) => ({ src: mod.default, name: toDisplayName(path) }))

// ── Takara project ──
const takaraGlob = import.meta.glob('../assets/projects/Takara/*', { eager: true })
const takaraSrc  = (name) => takaraGlob[`../assets/projects/Takara/${name}`]?.default ?? null

const takaraFolder = {
  type: 'folder',
  name: 'Takara',
  description: "A social media design project created for Takara, a Bitcoin-related brand. Developed a series of Instagram posts going through multiple design iterations before reaching the final version — combining Japanese-inspired aesthetics, gold typography, koi fish motifs and the brand tagline Built for Protection. Created using Adobe Photoshop and Clip Studio.",
  files: [
    {
      src: takaraSrc('Takara_Project.jpg'),
      name: 'Takara_Project.jpg',
      type: 'image',
      description: "A series of social media posts created for Takara, a Bitcoin-related brand, as part of a digital marketing campaign. Designed in Adobe Photoshop and Clip Studio, the posts went through multiple iterations exploring different visual directions — dark backgrounds, Japanese-inspired typography, koi fish motifs and gold detailing — before arriving at the final version. The final posts combine bold gold typography, the Japanese kanji for treasure (宝), and the tagline Built for Protection to communicate the brand's premium and secure identity across Instagram.",
    },
  ],
}

// ── National Plant a Flower Day project ──
const plantAFlowerDayFolder = {
  type: 'folder',
  name: 'National Plant a Flower Day',
  description: 'A content creation exercise completed as part of the Multimedia Design programme at SEA. Designed an infographic to raise awareness of National Plant a Flower Day on March 12 — combining illustrated elements, typography and information design to create an engaging and shareable piece of digital content.',
  files: [
    {
      src: march12Img,
      name: 'March_12_Infographic.jpg',
      type: 'image',
      description: 'A visually structured infographic created as a content creation exercise at SEA, designed to raise awareness of National Plant a Flower Day on March 12. Developed in the Content Creation class, the piece combines illustrated elements, typography and data visualisation to communicate the significance of the day in an engaging and shareable format.',
    },
  ],
}

// ── #GreenNorgesgade project ──
const greenNorgesgadeFolder = {
  type: 'folder',
  name: '#GreenNorgesgade',
  description: "A group project created as part of the Multimedia Design programme at SEA in collaboration with Esbjerg Municipality. Working as a team, we developed a creative campaign and idea catalogue proposing ways to make Norgesgade street in Esbjerg more green and eco-friendly. The project includes a full idea catalogue, a promotional poster and a social media campaign — all built around the #GreenNorgesgade campaign identity.",
  files: [
    {
      src: gnPdfUrl,
      name: 'GreenNorgesgade_Idea_Catalogue.pdf',
      type: 'pdf',
      description: 'A comprehensive idea catalogue created for Esbjerg Municipality as a group project, proposing concepts to transform Norgesgade into a greener and more eco-friendly street. Presented as a structured PDF document combining research, visual concepts and design proposals — developed as part of the Multimedia Design programme at SEA.',
    },
    {
      src: gnPosterImg,
      name: 'GreenNorgesgade_Poster.jpg',
      type: 'image',
      description: "A promotional poster designed to visually communicate the #GreenNorgesgade campaign concept. Created to accompany the idea catalogue and raise awareness of the green initiative, using environmental imagery and the campaign's hashtag-driven identity to engage the local community.",
    },
    {
      src: gnSoMe1Img,
      name: 'SoMe_post_1.jpg',
      type: 'image',
      description: 'First of three social media mockups created to promote the #GreenNorgesgade campaign. Shown as a phone screen mockup to demonstrate how the campaign content would appear in a real social media context — designed to drive community engagement around the green street initiative in Esbjerg.',
    },
    {
      src: gnSoMe2Img,
      name: 'SoMe_post_2.jpg',
      type: 'image',
      description: 'Second social media mockup in the #GreenNorgesgade campaign series. Continues the visual identity of the campaign while varying the content format to maintain engagement across posts.',
    },
    {
      src: gnSoMe3Img,
      name: 'SoMe_post_3.jpg',
      type: 'image',
      description: 'Third social media mockup in the #GreenNorgesgade campaign series. Completes the three-post series with a consistent visual language, designed to work together as a cohesive campaign set across digital platforms.',
    },
  ],
}

// ── Bugs project ──
const bugsGlob = import.meta.glob('../assets/projects/Bugs/*', { eager: true })
const bugsSrc  = (name) => bugsGlob[`../assets/projects/Bugs/${name}`]?.default ?? null

const bugsFolder = {
  type: 'folder',
  name: 'Bugs',
  description: "An illustration exercise completed as part of the Multimedia Design programme at SEA. Each student individually designed a bug illustration in Adobe Illustrator, which was then combined with two other group members' illustrations into a single collaborative poster — developing both individual illustration skills and the ability to work with combined assets in a shared design.",
  files: [
    {
      src: bugsSrc('Butterfly.jpg'),
      name: 'Butterfly.jpg',
      type: 'image',
      description: 'A detailed vector illustration of a butterfly created as an individual exercise in the Multimedia Design programme at SEA. Designed entirely in Adobe Illustrator, focusing on precision with the pen tool, shape building and color layering to achieve a naturalistic yet stylized insect illustration.',
    },
    {
      src: bugsSrc('Bug_Poster_Group_6.jpg'),
      name: 'Bug_Poster_Group_6.jpg',
      type: 'image',
      description: 'A collaborative poster combining three individual bug illustrations created by three group members. Each student contributed their own insect illustration — assembled together into a cohesive A-format poster layout in Adobe Illustrator. The exercise developed skills in both individual illustration and working with combined assets within a shared design brief.',
    },
  ],
}

// ── Halloween Drinks project ──
const halloweenGlob = import.meta.glob('../assets/projects/Halloween_Drinks_exercise/*', { eager: true })
const halloweenSrc  = (name) => halloweenGlob[`../assets/projects/Halloween_Drinks_exercise/${name}`]?.default ?? null

const halloweenDrinksFolder = {
  type: 'folder',
  name: 'Halloween Drinks',
  description: "A poster design exercise completed as part of the Multimedia Design programme at SEA. Tasked with creating Halloween-themed cocktail posters for two special drinks served at the university student bar — the Shark Attack and the Vampire Shot. All posters were designed in Adobe Illustrator, with two alternative versions produced for the Vampire Shot to explore different visual directions for the same brief.",
  files: [
    {
      src: halloweenSrc('Shark_Attack.png'),
      name: 'Shark_Attack.png',
      type: 'image',
      description: "A Halloween-themed cocktail poster for the Shark Attack drink at the university student bar. Designed in Adobe Illustrator using a retro movie poster style inspired by classic shark films — bold typography, dramatic underwater imagery and a dark atmospheric color palette to capture the drink's fearsome name.",
    },
    {
      src: halloweenSrc('Vampire_shot_1.png'),
      name: 'Vampire_shot_1.png',
      type: 'image',
      description: "First version of a Halloween cocktail poster for the Vampire Shot at the university student bar. Created in Adobe Illustrator with a dark gothic aesthetic — deep reds, dramatic lighting and vampire-themed imagery to match the drink's blood-red appearance and Halloween theme.",
    },
    {
      src: halloweenSrc('Vampire_shot_2.png'),
      name: 'Vampire_shot_2.png',
      type: 'image',
      description: 'An alternative version of the Vampire Shot poster exploring a different visual direction. Maintains the gothic Halloween mood while varying the composition, color treatment and typographic approach — created to present two distinct design solutions for the same brief.',
    },
  ],
}

// ── International Day project ──
const intlDayGlob = import.meta.glob('../assets/projects/International_Day/*', { eager: true })
const intlDaySrc  = (name) => intlDayGlob[`../assets/projects/International_Day/${name}`]?.default ?? null

const internationalDayFolder = {
  type: 'folder',
  name: 'International Day',
  description: "A content creation exercise completed as part of the Multimedia Design programme at SEA. Designed two versions of a promotional poster for the university's International Day event — an A4 print format and a web banner format — both created in Adobe Photoshop.",
  files: [
    {
      src: intlDaySrc('International_Day_A4.jpg'),
      name: 'International_Day_A4.jpg',
      type: 'image',
      description: "A print-ready A4 poster designed for the International Day event at SEA. Created in Adobe Photoshop, the design combines atmospheric cityscape imagery with typographic elements to produce a visually striking poster suited for physical display on campus.",
    },
    {
      src: intlDaySrc('International_Day_Web.png'),
      name: 'International_Day_Web.png',
      type: 'image',
      description: "A web-optimized banner version of the International Day poster, adapted from the A4 format for digital display. Resized and reformatted in Adobe Photoshop to suit online platforms and screen viewing while maintaining the original design's visual identity.",
    },
  ],
}

// ── 230 Racing project ──
const racingGlob = import.meta.glob('../assets/projects/230Racing/*', { eager: true })
const racingSrc  = (name) => racingGlob[`../assets/projects/230Racing/${name}`]?.default ?? null

const racing230Folder = {
  type: 'folder',
  name: '230 Racing',
  description: "A complete brand identity project created as a group assignment at SEA — Syddansk Erhvervsakademi. Developed a full visual identity for 230 Racing, a fictional motorsport team, including logo design, promotional materials, merchandise concepts and a functional website. All brand assets were built around a custom illustrated mascot character to create a bold, energetic and consistent brand presence.",
  files: [
    {
      src: racingSrc('Logo.jpg'),
      name: 'Logo.jpg',
      type: 'image',
      description: 'The core brand identity mark for 230 Racing — a custom illustrated mascot character combined with stylized racing numbers, created entirely in Adobe Illustrator. Designed to be versatile across digital and print applications while communicating speed, energy and a bold team personality.',
    },
    {
      src: racingSrc('Banner.jpg'),
      name: 'Banner.jpg',
      type: 'image',
      description: 'A vertical promotional banner designed for event display and physical print use. Created in Adobe Illustrator, it integrates the brand mascot, logo, photography and typographic elements into a cohesive layout that captures the racing spirit of the 230 brand.',
    },
    {
      src: racingSrc('Website.jpg'),
      name: 'Website.jpg',
      type: 'image',
      description: "A fully designed and functional promotional website for 230 Racing. Features a hero section, rider profiles, upcoming events and social media integration — built to reflect the brand's high-energy identity while providing key information to fans and sponsors.",
    },
    {
      src: racingSrc('Merch_1.jpg'),
      name: 'Merch_1.jpg',
      type: 'image',
      description: 'A branded merchandise mockup featuring a t-shirt and cap with the 230 Racing logo applied. Created to demonstrate how the brand identity translates onto physical fan merchandise and team apparel.',
    },
    {
      src: racingSrc('Merch_2.jpg'),
      name: 'Merch_2.jpg',
      type: 'image',
      description: "A branded tote bag mockup showcasing the 230 Racing mascot logo on a canvas bag. Demonstrates the brand's versatility across everyday lifestyle merchandise beyond racing-specific gear.",
    },
    {
      src: racingSrc('Merch_3.jpg'),
      name: 'Merch_3.jpg',
      type: 'image',
      description: 'A phone case sticker mockup featuring the 230 Racing logo applied to a device. Shows the brand working at small scale on personal accessories, extending the identity into everyday consumer products.',
    },
  ],
}

// ── Brothers Lionheart project ──
const blhGlob = import.meta.glob('../assets/projects/The_Brothers_Lionheart/*', { eager: true })
const blhSrc  = (name) => blhGlob[`../assets/projects/The_Brothers_Lionheart/${name}`]?.default ?? null

const brothersLionheartFolder = {
  type: 'folder',
  name: 'The Brothers Lionheart',
  description: "A complete marketing campaign and promotional package created for Skarntyden Amateur Theatre Group's winter production of The Brothers Lionheart. Includes a functional website, responsive prototype, promotional video and a full suite of digital print productions — all developed as a group exam project as part of the Multimedia Design programme at SEA.",
  files: [
    {
      src: blhSrc('Bifold_Leaflet.jpeg'),
      name: 'Bifold_Leaflet.jpeg',
      type: 'image',
      description: 'A two-panel printed leaflet designed to promote the winter production. Combines atmospheric imagery with event details, structured for easy readability and distribution at local venues.',
    },
    {
      src: blhSrc('Infographic.jpg'),
      name: 'Infographic.jpg',
      type: 'image',
      description: "A visual infographic presenting key information about the production in an engaging format. Designed to be shared digitally and printed, balancing data clarity with the campaign's visual identity.",
    },
    {
      src: blhSrc('Poster.jpg'),
      name: 'Poster.jpg',
      type: 'image',
      description: "A promotional poster created for physical display in public spaces. Features the campaign's core visual language — typography, color palette and imagery — to grab attention and communicate the event at a glance.",
    },
    {
      src: blhSrc('SoMe_post_1.jpg'),
      name: 'SoMe_post_1.jpg',
      type: 'image',
      description: 'Part of a three-post social media series for Instagram and Facebook. Event announcement format — introduces the production with the core campaign visuals.',
    },
    {
      src: blhSrc('SoMe_post_2.jpg'),
      name: 'SoMe_post_2.jpg',
      type: 'image',
      description: 'Part of a three-post social media series for Instagram and Facebook. Countdown format — builds anticipation leading up to the show date.',
    },
    {
      src: blhSrc('SoMe_post_3.jpg'),
      name: 'SoMe_post_3.jpg',
      type: 'image',
      description: 'Part of a three-post social media series for Instagram and Facebook. Audience engagement format — designed to encourage sharing and interaction.',
    },
    {
      src: blhSrc('Stickers_1.jpg'),
      name: 'Stickers_1.jpg',
      type: 'image',
      description: 'Branded sticker designs created as supplementary promotional merchandise. Designed to work both digitally and as physical prints, extending the campaign identity into everyday objects.',
    },
    {
      src: blhSrc('Stickers_2.jpg'),
      name: 'Stickers_2.jpg',
      type: 'image',
      description: "Second set of branded sticker designs. Extends the merchandise range while maintaining the campaign's visual consistency.",
    },
    {
      src: blhSrc('Website.jpg'),
      name: 'Website.jpg',
      type: 'image',
      description: 'A fully functional promotional website built for the theatre production. Includes event information, responsive design, and is developed using HTML, CSS and JavaScript with GitHub for version control.',
      link: { text: 'Visit Live Site', url: 'https://karvin01.github.io/Semester-project-webpage/index.html' },
    },
    {
      src: 'https://img.youtube.com/vi/4jDPY7XIhOw/hqdefault.jpg',
      name: 'Video.mp4',
      type: 'youtube',
      youtubeUrl: 'https://youtu.be/4jDPY7XIhOw',
      description: 'A short promotional video produced to build anticipation for the winter production. Combines motion graphics, campaign visuals and atmospheric sound design to capture the tone and story of The Brothers Lionheart.',
    },
    {
      src: blhSrc('Assets.jpg'),
      name: 'Assets.jpg',
      type: 'image',
      description: 'A collection of brand assets produced during the campaign — including UI elements, color swatches, typography guides and supporting graphics used across all deliverables to maintain visual consistency.',
    },
  ],
}

// ── Illustration descriptions — add/edit descriptions here ──
const illDescriptions = {
  'characters':              'Characters for a story I am writing',
  'Dinkle Binkle':           'Dinkle Binkle — original character',
  'Dinkle Binkle 2':        'Dinkle Binkle — alternate design',
  'storyboard':              'Storyboard panels',
  'life drawing female prop':'Life drawing study with props',
  'priest Josiah portrait':  'Portrait of Priest Josiah',
}

// ── Illustrations ──
const illGlob = import.meta.glob('../assets/illustrations/*.{jpg,png}', { eager: true })
const illustrationImages = Object.entries(illGlob)
  .sort(([a], [b]) => a.localeCompare(b))
  .map(([path, mod]) => {
    const raw  = path.split('/').pop().replace(/\.[^.]+$/, '')
    const name = raw.replace(/\b\w/g, c => c.toUpperCase()) + '.jpg'
    const description = illDescriptions[raw] ?? null
    return { src: mod.default, name, description }
  })

const headingRef     = ref(null)
const cardsRef       = ref(null)
const headingVisible = ref(false)
const cardsVisible   = ref(false)

// Modal state
const modalOpen  = ref(false)
const activeItem = ref({ label: '', images: [] })

const openModal = (item) => {
  activeItem.value = item
  modalOpen.value  = true
}

const makeObserver = (visibleRef, threshold) => {
  let initial = true
  return new IntersectionObserver(([entry]) => {
    if (initial) { initial = false; if (entry.isIntersecting && window.scrollY < 50) return }
    visibleRef.value = entry.isIntersecting
  }, { threshold, rootMargin: '0px 0px -60px 0px' })
}

onMounted(() => {
  makeObserver(headingVisible, 0.3).observe(headingRef.value)
  makeObserver(cardsVisible,   0.2).observe(cardsRef.value)
})

const items = [
  { label: 'PHOTOGRAPHY',   image: cameraImg, images: photographyImages },
  { label: 'PROJECTS',      image: webImg,    images: [
    { src: miniMeImg, name: 'mini me.jpg', type: 'image', description: 'A self-portrait illustration created as a character design exercise in the Multimedia Design programme at SEA. Designed entirely in Adobe Illustrator, the task was to recreate yourself as a stylized vector character — developing skills in digital illustration, shape building and working with the Illustrator pen and shape tools to achieve a cohesive personal likeness.' },
  ], folders: [racing230Folder, brothersLionheartFolder, internationalDayFolder, halloweenDrinksFolder, bugsFolder, greenNorgesgadeFolder, plantAFlowerDayFolder, takaraFolder] },
  { label: 'ILLUSTRATIONS', image: illImg,    images: illustrationImages },
  { label: 'VIDEO',         image: brandImg,  images: [], comingSoon: true },
]
</script>

<style scoped>
/* ── Levitate ── */
@keyframes levitate {
  0%, 100% { transform: translateY(0); }
  50%       { transform: translateY(-18px); }
}

.levitate {
  animation: levitate 3.5s ease-in-out infinite;
}

/* ── Scale on hover (outer wrapper) ── */
.card-scale {
  transition: transform 0.3s ease;
}
.card-scale:hover {
  transform: scale(1.08);
}

/* ── Drop-bounce entrance ── */
@keyframes drop-bounce {
  0%   { opacity: 0; transform: translateY(-80px); }
  60%  { opacity: 1; transform: translateY(8px);   }
  78%  { transform: translateY(-6px); }
  90%  { transform: translateY(3px);  }
  100% { transform: translateY(0);    }
}

.levitate {
  animation: drop-bounce 0.7s cubic-bezier(0.22, 1, 0.36, 1) forwards,
             levitate    3.5s ease-in-out infinite 0.7s;
  will-change: transform;
  backface-visibility: hidden;
  transform: translateZ(0);
}


/* ── Shadow under icons — pulses opposite to levitate ── */
@keyframes shadow-pulse {
  0%, 100% { transform: scaleX(0.6); opacity: 0.2;  filter: blur(10px); }
  50%       { transform: scaleX(1);   opacity: 0.45; filter: blur(6px);  }
}

.shadow-pulse {
  width: 80%;
  height: 24px;
  background: rgba(22, 76, 149, 0.35);
  border-radius: 50%;
  margin: 10px auto 0;
  filter: blur(8px);
  animation: shadow-pulse 3.5s ease-in-out infinite;
}

/* ── Responsive ── */
@media (min-width: 769px) and (max-width: 1024px) {
  .col-start-2.col-span-10 h2 { font-size: 2.8rem !important; }
  /* Tighten card gap so 4 cards fit without overflow */
  .grid.grid-cols-4 { gap: 1.2rem !important; }
}

@media (max-width: 768px) {
  .grid.grid-cols-12 { display: flex !important; flex-direction: column !important; padding: 0 1rem !important; }
  .col-start-2.col-span-10 { width: 100% !important; }
  .grid.grid-cols-4 { grid-template-columns: 1fr 1fr !important; gap: 1rem !important; }
  .col-start-2.col-span-10 h2 { font-size: 2.2rem !important; margin-top: 2rem !important; }
}

/* ── Shine sweep on hover ── */
@keyframes shine-sweep {
  0%   { left: -80%; }
  100% { left: 130%; }
}

.shine {
  position: absolute;
  top: 0; left: -80%;
  width: 50%;
  height: 100%;
  background: linear-gradient(
    120deg,
    transparent 0%,
    rgba(255,255,255,0.55) 50%,
    transparent 100%
  );
  pointer-events: none;
  opacity: 0;
  transition: opacity 0.1s;
}

.shine-wrap:hover .shine {
  opacity: 1;
  animation: shine-sweep 0.65s ease forwards;
}
</style>
