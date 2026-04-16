<template>
  <Teleport to="body">
    <div v-if="modelValue" class="xp-overlay" @mousedown.self="close">
      <div class="xp-window" :style="windowStyle" @mousedown.stop>

        <!-- ── Title bar ── -->
        <div class="xp-titlebar" @mousedown="startDrag">
          <div class="xp-title-left">
            <svg width="16" height="16" viewBox="0 0 16 16" style="flex-shrink:0;">
              <rect x="1" y="4" width="14" height="10" rx="1" fill="#f5c400" stroke="#c89000" stroke-width="1"/>
              <rect x="1" y="3" width="7" height="3" rx="1" fill="#f5c400" stroke="#c89000" stroke-width="1"/>
            </svg>
            <span class="xp-title-text">{{ activeCategoryLabel }}</span>
          </div>
          <div class="xp-btns">
            <button class="xp-btn xp-btn-min" @mousedown.stop @click.stop title="Minimize">
              <svg width="7" height="2" viewBox="0 0 7 2"><rect width="7" height="2" fill="white"/></svg>
            </button>
            <button class="xp-btn xp-btn-max" @mousedown.stop @click.stop title="Maximize">
              <svg width="8" height="8" viewBox="0 0 8 8"><rect x="0.5" y="0.5" width="7" height="7" fill="none" stroke="white" stroke-width="1.5"/><rect x="0.5" y="0.5" width="7" height="2" fill="white"/></svg>
            </button>
            <button class="xp-btn xp-btn-close" @mousedown.stop @click.stop="close" title="Close">
              <svg width="8" height="8" viewBox="0 0 8 8">
                <line x1="0.5" y1="0.5" x2="7.5" y2="7.5" stroke="white" stroke-width="1.8" stroke-linecap="round"/>
                <line x1="7.5" y1="0.5" x2="0.5" y2="7.5" stroke="white" stroke-width="1.8" stroke-linecap="round"/>
              </svg>
            </button>
          </div>
        </div>

        <!-- ── Menu bar ── -->
        <div class="xp-menubar">
          <span v-for="item in ['File','Edit','View','Favorites','Help']" :key="item" class="xp-menu-item">{{ item }}</span>
        </div>

        <!-- ── Toolbar ── -->
        <div class="xp-toolbar">
          <button class="xp-tool-btn" title="Back" @click="navigateBack" :style="{ opacity: currentFolder ? 1 : 0.4 }">
            <svg width="16" height="16" viewBox="0 0 16 16"><path fill="#444" d="M10 3L5 8l5 5V3z"/></svg>
          </button>
          <button class="xp-tool-btn" title="Forward" style="opacity:0.4;">
            <svg width="16" height="16" viewBox="0 0 16 16"><path fill="#444" d="M6 3l5 5-5 5V3z"/></svg>
          </button>
          <button class="xp-tool-btn" title="Up">
            <svg width="16" height="16" viewBox="0 0 16 16"><path fill="#444" d="M8 3l-5 6h3v4h4V9h3L8 3z"/></svg>
          </button>
          <div class="xp-toolbar-sep"/>
          <button class="xp-tool-btn" title="Folders">
            <svg width="16" height="16" viewBox="0 0 16 16"><rect x="1" y="5" width="14" height="9" rx="1" fill="#f5c400" stroke="#c89000" stroke-width="1"/><rect x="1" y="4" width="6" height="3" rx="1" fill="#f5c400" stroke="#c89000" stroke-width="1"/></svg>
            <span class="xp-tool-label">Folders</span>
          </button>
        </div>

        <!-- ── Address bar ── -->
        <div class="xp-addrbar">
          <span class="xp-addr-label">Address</span>
          <div class="xp-addr-input">
            <svg width="14" height="14" viewBox="0 0 16 16" style="flex-shrink:0;"><rect x="1" y="4" width="14" height="10" rx="1" fill="#f5c400" stroke="#c89000" stroke-width="1"/><rect x="1" y="3" width="7" height="3" rx="1" fill="#f5c400" stroke="#c89000" stroke-width="1"/></svg>
            <span>My Documents\Portfolio\{{ activeCategoryLabel }}{{ currentFolder ? '\u005c' + currentFolder.name : '' }}</span>
          </div>
          <button class="xp-go-btn">Go</button>
        </div>

        <!-- ── Main area ── -->
        <div class="xp-body">

          <!-- Left sidebar -->
          <div class="xp-sidebar">

            <!-- Other Places = category switcher -->
            <div class="xp-sidebar-section">
              <div class="xp-sidebar-header">Other Places</div>
              <div class="xp-sidebar-links">
                <a
                  v-for="cat in categories"
                  :key="cat.label"
                  class="xp-sidebar-link"
                  :class="{ 'xp-sidebar-link-active': cat.label === activeCategoryLabel }"
                  @click="switchCategory(cat.label)"
                >
                  <svg width="14" height="14" viewBox="0 0 16 16"><rect x="1" y="4" width="14" height="10" rx="1" fill="#f5c400" stroke="#c89000" stroke-width="1"/><rect x="1" y="3" width="7" height="3" rx="1" fill="#f5c400" stroke="#c89000" stroke-width="1"/></svg>
                  {{ cat.label.charAt(0) + cat.label.slice(1).toLowerCase() }}
                </a>
              </div>
            </div>

            <!-- Details panel -->
            <div class="xp-sidebar-section" v-if="selectedItem">
              <div class="xp-sidebar-header">Details</div>
              <div class="xp-sidebar-detail">
                <!-- Folder icon -->
                <div class="xp-detail-thumb xp-detail-folder-thumb" v-if="selectedItem.type === 'folder'">
                  <svg width="56" height="48" viewBox="0 0 64 54" fill="none">
                    <rect x="1" y="14" width="62" height="38" rx="3" fill="#f5c400" stroke="#c89000" stroke-width="2"/>
                    <rect x="1" y="8" width="26" height="12" rx="3" fill="#f5c400" stroke="#c89000" stroke-width="2"/>
                    <rect x="5" y="20" width="54" height="28" rx="2" fill="#fffbe6"/>
                  </svg>
                </div>
                <!-- PDF icon -->
                <div class="xp-detail-thumb xp-detail-folder-thumb" v-else-if="selectedItem.type === 'pdf'">
                  <svg width="40" height="50" viewBox="0 0 52 64" fill="none">
                    <rect x="1" y="1" width="50" height="62" rx="3" fill="white" stroke="#c00" stroke-width="2"/>
                    <path d="M31 1v16h16" fill="none" stroke="#c00" stroke-width="2"/>
                    <path d="M31 1l16 16" fill="#fdd" stroke="#c00" stroke-width="2"/>
                    <text x="8" y="48" font-family="Arial" font-weight="800" font-size="13" fill="#c00">PDF</text>
                  </svg>
                </div>
                <!-- Video thumbnail -->
                <div class="xp-detail-thumb" v-else-if="selectedItem.type === 'video'">
                  <video :src="selectedItem.src" muted preload="metadata" style="max-width:100%;max-height:100%;object-fit:cover;" />
                </div>
                <!-- Image thumbnail -->
                <div class="xp-detail-thumb" v-else style="background:#e8f0f8;">
                  <img
                    :src="selectedItem.src"
                    :alt="selectedItem.name"
                    loading="lazy"
                    style="filter:blur(8px);transition:filter 0.4s ease;"
                    @load="$event.target.style.filter='none'"
                  />
                </div>

                <p class="xp-detail-name">{{ selectedItem.name }}</p>
                <p class="xp-detail-type">{{ selectedItem.type === 'folder' ? 'File Folder' : selectedItem.type === 'pdf' ? 'PDF Document' : selectedItem.type === 'video' ? 'Video File' : 'Image File' }}</p>

                <!-- Open PDF button -->
                <a
                  v-if="selectedItem.type === 'pdf'"
                  :href="selectedItem.src"
                  target="_blank"
                  rel="noopener noreferrer"
                  class="xp-detail-link"
                >Open PDF</a>

                <!-- Live site link (e.g. Website.png) -->
                <a
                  v-if="selectedItem.link"
                  :href="selectedItem.link.url"
                  target="_blank"
                  rel="noopener noreferrer"
                  class="xp-detail-link"
                >{{ selectedItem.link.text }}</a>

                <p v-if="selectedItem.description" class="xp-detail-desc">
                  {{ selectedItem.description }}
                </p>
              </div>
            </div>

          </div>

          <!-- Content area -->
          <div class="xp-content">
            <!-- Coming soon placeholder -->
            <div v-if="activeCategory?.comingSoon && !currentFolder" class="xp-coming-soon">
              <svg xmlns="http://www.w3.org/2000/svg" width="64" height="64" viewBox="0 0 24 24" style="opacity:0.25;margin-bottom:1rem;">
                <path fill="#164C95" d="m10 15.586l-3.293-3.293l-1.414 1.414L10 18.414l9.707-9.707l-1.414-1.414z"/>
                <path fill="#164C95" d="M12 2C6.486 2 2 6.486 2 12s4.486 10 10 10s10-4.486 10-10S17.514 2 12 2m0 18c-4.411 0-8-3.589-8-8s3.589-8 8-8s8 3.589 8 8s-3.589 8-8 8"/>
              </svg>
              <p style="font-family:'Poppins',sans-serif;font-weight:700;font-size:1.1rem;color:#164C95;margin:0;">Coming Soon</p>
              <p style="font-family:'Montserrat',sans-serif;font-size:0.8rem;color:#6D9FE7;margin-top:0.3rem;">This section is currently being worked on.</p>
            </div>
            <div v-else-if="currentItems.length" class="xp-grid">
              <div
                v-for="(item, i) in currentItems"
                :key="i"
                class="xp-item"
                :class="{ 'xp-item-selected': selectedIndex === i }"
                @click="handleItemClick(i)"
                @dblclick="item.type === 'folder' ? openFolder(item) : item.type === 'pdf' ? openPdf(item) : openViewer(i)"
                @touchend.prevent="handleTap(i, $event)"
              >
                <!-- Folder icon -->
                <div class="xp-thumb xp-folder-thumb" v-if="item.type === 'folder'">
                  <svg width="64" height="54" viewBox="0 0 64 54" fill="none">
                    <rect x="1" y="14" width="62" height="38" rx="3" fill="#f5c400" stroke="#c89000" stroke-width="2"/>
                    <rect x="1" y="8" width="26" height="12" rx="3" fill="#f5c400" stroke="#c89000" stroke-width="2"/>
                    <rect x="5" y="20" width="54" height="28" rx="2" fill="#fffbe6"/>
                  </svg>
                </div>
                <!-- PDF icon -->
                <div class="xp-thumb xp-folder-thumb" v-else-if="item.type === 'pdf'">
                  <svg width="52" height="64" viewBox="0 0 52 64" fill="none">
                    <rect x="1" y="1" width="50" height="62" rx="3" fill="white" stroke="#c00" stroke-width="2"/>
                    <path d="M31 1v16h16" fill="none" stroke="#c00" stroke-width="2"/>
                    <path d="M31 1l16 16" fill="#fdd" stroke="#c00" stroke-width="2"/>
                    <text x="8" y="48" font-family="Arial" font-weight="800" font-size="13" fill="#c00">PDF</text>
                  </svg>
                </div>
                <!-- Video thumbnail -->
                <div class="xp-thumb" v-else-if="item.type === 'video'">
                  <video :src="item.src" muted preload="metadata" draggable="false" style="max-width:100%;max-height:100%;object-fit:cover;" />
                </div>
                <!-- Image thumbnail -->
                <div class="xp-thumb" v-else style="background:#e8f0f8;">
                  <img
                    :src="item.src"
                    :alt="item.name"
                    draggable="false"
                    loading="lazy"
                    style="filter:blur(8px);transition:filter 0.4s ease;"
                    @load="$event.target.style.filter='none'"
                  />
                </div>
                <span class="xp-filename">{{ item.name }}</span>
              </div>
            </div>
            <div v-else class="xp-empty">
              <svg width="64" height="64" viewBox="0 0 64 64">
                <rect x="4" y="18" width="56" height="40" rx="2" fill="#f5c400" stroke="#c89000" stroke-width="2"/>
                <rect x="4" y="14" width="26" height="10" rx="2" fill="#f5c400" stroke="#c89000" stroke-width="2"/>
                <rect x="8" y="28" width="48" height="26" rx="1" fill="#fffbe6"/>
              </svg>
              <p class="xp-empty-text">This folder is empty.</p>
              <p class="xp-empty-hint">Images will appear here once added.</p>
            </div>
          </div>
        </div>

        <!-- ── Status bar ── -->
        <div class="xp-statusbar">
          <span class="xp-status-seg">{{ currentItems.length }} object(s)</span>
          <div class="xp-status-grip">
            <svg width="12" height="12" viewBox="0 0 12 12">
              <line x1="9" y1="3" x2="3" y2="9" stroke="#999" stroke-width="1"/>
              <line x1="11" y1="5" x2="5" y2="11" stroke="#999" stroke-width="1"/>
              <line x1="11" y1="8" x2="8" y2="11" stroke="#999" stroke-width="1"/>
            </svg>
          </div>
        </div>

      </div>
    </div>

    <!-- ── Windows Picture and Fax Viewer ── -->
    <div v-if="viewerOpen && viewerImage" class="xp-viewer-window" :style="viewerStyle" @mousedown.stop>
      <div class="xp-titlebar xp-viewer-titlebar" @mousedown="startViewerDrag">
        <div class="xp-title-left">
          <svg width="16" height="16" viewBox="0 0 16 16" style="flex-shrink:0;">
            <rect x="1" y="2" width="14" height="12" rx="1" fill="#b0d0f8" stroke="#5090d0" stroke-width="1"/>
            <rect x="3" y="4" width="10" height="8" rx="1" fill="white" stroke="#a0c0e8" stroke-width="1"/>
            <path d="M3 10 L6 7 L9 9 L11 7 L13 10 Z" fill="#60a040"/>
            <circle cx="10" cy="6" r="1.5" fill="#f0c030"/>
          </svg>
          <span class="xp-title-text">{{ viewerImage.name }} — Windows Picture and Fax Viewer</span>
        </div>
        <div class="xp-btns">
          <button class="xp-btn xp-btn-min" @mousedown.stop @click.stop title="Minimize">
            <svg width="7" height="2" viewBox="0 0 7 2"><rect width="7" height="2" fill="white"/></svg>
          </button>
          <button class="xp-btn xp-btn-max" @mousedown.stop @click.stop title="Maximize">
            <svg width="8" height="8" viewBox="0 0 8 8"><rect x="0.5" y="0.5" width="7" height="7" fill="none" stroke="white" stroke-width="1.5"/><rect x="0.5" y="0.5" width="7" height="2" fill="white"/></svg>
          </button>
          <button class="xp-btn xp-btn-close" @mousedown.stop @click.stop="viewerOpen = false" title="Close">
            <svg width="8" height="8" viewBox="0 0 8 8">
              <line x1="0.5" y1="0.5" x2="7.5" y2="7.5" stroke="white" stroke-width="1.8" stroke-linecap="round"/>
              <line x1="7.5" y1="0.5" x2="0.5" y2="7.5" stroke="white" stroke-width="1.8" stroke-linecap="round"/>
            </svg>
          </button>
        </div>
      </div>

      <div
        class="xp-viewer-image-area"
        :style="{ cursor: viewerZoom > 1 ? (isPanning ? 'grabbing' : 'grab') : 'default' }"
        @mousedown.stop="viewerZoom > 1 && (isPanning = true, panStart = { x: $event.clientX, y: $event.clientY })"
      >
        <video
          v-if="viewerImage.type === 'video'"
          :src="viewerImage.src"
          controls
          style="max-width:100%;max-height:440px;outline:none;"
        />
        <img
          v-else
          :src="viewerImage.src"
          :alt="viewerImage.name"
          class="xp-viewer-img"
          draggable="false"
          loading="lazy"
          :style="{
            transform:  viewerImgTransform,
            transition: isPanning ? 'none' : (viewerImageLoaded ? 'transform 0.25s ease' : 'transform 0.25s ease, filter 0.4s ease'),
            filter:     viewerImageLoaded ? 'none' : 'blur(8px)',
          }"
          @load="viewerImageLoaded = true"
        />
      </div>

      <div class="xp-viewer-toolbar">
        <button class="xp-viewer-btn" :disabled="viewerIndex <= 0" @click="viewerIndex--" title="Previous">
          <svg width="16" height="16" viewBox="0 0 16 16"><path d="M10 3L4 8l6 5z" :fill="viewerIndex <= 0 ? '#aaa' : '#333'"/></svg>
        </button>
        <button class="xp-viewer-btn" :disabled="viewerIndex >= viewableItems.length - 1" @click="viewerIndex++" title="Next">
          <svg width="16" height="16" viewBox="0 0 16 16"><path d="M6 3l6 5-6 5z" :fill="viewerIndex >= viewableItems.length - 1 ? '#aaa' : '#333'"/></svg>
        </button>
        <div class="xp-viewer-sep"/>
        <button class="xp-viewer-btn" title="Rotate CW" @click="viewerRotation += 90">
          <svg width="16" height="16" viewBox="0 0 16 16"><path d="M13 6A6 6 0 1 0 10 12" fill="none" stroke="#333" stroke-width="1.5"/><path d="M13 3v4h-4" fill="none" stroke="#333" stroke-width="1.5"/></svg>
        </button>
        <button class="xp-viewer-btn" title="Rotate CCW" @click="viewerRotation -= 90">
          <svg width="16" height="16" viewBox="0 0 16 16"><path d="M3 6A6 6 0 1 1 6 12" fill="none" stroke="#333" stroke-width="1.5"/><path d="M3 3v4h4" fill="none" stroke="#333" stroke-width="1.5"/></svg>
        </button>
        <div class="xp-viewer-sep"/>
        <button class="xp-viewer-btn" title="Zoom in" @click="viewerZoom = Math.min(viewerZoom + 0.25, 4)">
          <svg width="16" height="16" viewBox="0 0 16 16"><circle cx="7" cy="7" r="5" fill="none" stroke="#333" stroke-width="1.5"/><line x1="7" y1="4" x2="7" y2="10" stroke="#333" stroke-width="1.5"/><line x1="4" y1="7" x2="10" y2="7" stroke="#333" stroke-width="1.5"/><line x1="11" y1="11" x2="14" y2="14" stroke="#333" stroke-width="2"/></svg>
        </button>
        <button class="xp-viewer-btn" title="Zoom out" @click="viewerZoom = Math.max(viewerZoom - 0.25, 0.25); if (viewerZoom <= 1) viewerPan = { x: 0, y: 0 }">
          <svg width="16" height="16" viewBox="0 0 16 16"><circle cx="7" cy="7" r="5" fill="none" stroke="#333" stroke-width="1.5"/><line x1="4" y1="7" x2="10" y2="7" stroke="#333" stroke-width="1.5"/><line x1="11" y1="11" x2="14" y2="14" stroke="#333" stroke-width="2"/></svg>
        </button>
        <div class="xp-viewer-sep"/>
        <span class="xp-viewer-counter">{{ viewerIndex + 1 }} / {{ viewableItems.length }}</span>
      </div>
    </div>

  <!-- ── Mobile details bottom panel ── -->
  <Transition name="xp-slide-up">
    <div
      v-if="modelValue && selectedItem"
      class="xp-mobile-details"
      @click.stop
    >
      <div class="xp-mobile-details-bar">
        <span class="xp-mobile-details-title">Details</span>
        <button class="xp-mobile-details-close" @click="selectedIndex = null">✕</button>
      </div>
      <div class="xp-mobile-details-body">
        <p class="xp-mobile-detail-name">{{ selectedItem.name }}</p>
        <p class="xp-mobile-detail-type">{{ selectedItem.type === 'folder' ? 'File Folder' : selectedItem.type === 'pdf' ? 'PDF Document' : selectedItem.type === 'video' ? 'Video File' : 'Image File' }}</p>
        <a
          v-if="selectedItem.type === 'pdf'"
          :href="selectedItem.src"
          target="_blank"
          rel="noopener noreferrer"
          class="xp-detail-link"
          style="align-self:flex-start;"
        >Open PDF</a>
        <a
          v-if="selectedItem.link"
          :href="selectedItem.link.url"
          target="_blank"
          rel="noopener noreferrer"
          class="xp-detail-link"
        >{{ selectedItem.link.text }}</a>
        <p v-if="selectedItem.description" class="xp-mobile-detail-desc">{{ selectedItem.description }}</p>
      </div>
    </div>
  </Transition>

  </Teleport>
</template>

<script setup>
import { ref, computed, watch, onMounted, onUnmounted } from 'vue'

const props = defineProps({
  modelValue:      Boolean,
  categories:      { type: Array,  default: () => [] },
  initialCategory: { type: String, default: '' },
})
const emit = defineEmits(['update:modelValue'])

const close = () => emit('update:modelValue', false)

// ── Active category ──
const activeCategoryLabel = ref('')

const activeCategory = computed(() =>
  props.categories.find(c => c.label === activeCategoryLabel.value) ?? props.categories[0] ?? null
)

// ── Folder navigation ──
const currentFolder = ref(null)

const currentItems = computed(() => {
  if (currentFolder.value) return currentFolder.value.files ?? []
  const cat = activeCategory.value
  if (!cat) return []
  return [...(cat.folders ?? []), ...(cat.images ?? [])]
})

// Items openable in the viewer (images and videos only)
const viewableItems = computed(() =>
  currentItems.value.filter(item => item.type !== 'folder' && item.type !== 'pdf' && item.src)
)

const openPdf = (item) => {
  if (item.src) window.open(item.src, '_blank', 'noopener,noreferrer')
}

const selectedItem = computed(() =>
  selectedIndex.value !== null ? (currentItems.value[selectedIndex.value] ?? null) : null
)

const switchCategory = (label) => {
  activeCategoryLabel.value = label
  selectedIndex.value = null
  viewerOpen.value = false
  currentFolder.value = null
  tapState.value = { index: -1, time: 0 }
}

// ── File browser window ──
const pos           = ref({ x: 0, y: 0 })
const isDragging    = ref(false)
const dragOffset    = ref({ x: 0, y: 0 })
const selectedIndex = ref(null)

const W = 820
const H = 540

watch(() => props.modelValue, (val) => {
  if (val) {
    activeCategoryLabel.value = props.initialCategory || props.categories[0]?.label || ''
    pos.value = {
      x: Math.max(20, (window.innerWidth  - W) / 2),
      y: Math.max(20, (window.innerHeight - H) / 2),
    }
    selectedIndex.value = null
    viewerOpen.value    = false
    currentFolder.value = null
    tapState.value      = { index: -1, time: 0 }
  }
})

const windowStyle = computed(() => ({
  left:   pos.value.x + 'px',
  top:    pos.value.y + 'px',
  width:  W + 'px',
  height: H + 'px',
}))

const startDrag = (e) => {
  isDragging.value = true
  dragOffset.value = { x: e.clientX - pos.value.x, y: e.clientY - pos.value.y }
}

// ── Picture viewer ──
const VW = 700
const VH = 560

const viewerOpen        = ref(false)
const viewerIndex       = ref(0)
const viewerImageLoaded = ref(false)

watch(viewerIndex, () => { viewerImageLoaded.value = false })
watch(viewerOpen,  (val) => { if (!val) viewerImageLoaded.value = false })
const viewerPos      = ref({ x: 0, y: 0 })
const viewerDragging = ref(false)
const viewerDragOff  = ref({ x: 0, y: 0 })
const viewerRotation = ref(0)
const viewerZoom     = ref(1)
const viewerPan      = ref({ x: 0, y: 0 })
const isPanning      = ref(false)
const panStart       = ref({ x: 0, y: 0 })

const viewerImage        = computed(() => viewableItems.value[viewerIndex.value] ?? null)
const viewerImgTransform = computed(() =>
  `translate(${viewerPan.value.x}px, ${viewerPan.value.y}px) rotate(${viewerRotation.value}deg) scale(${viewerZoom.value})`
)

const viewerStyle = computed(() => ({
  left:   viewerPos.value.x + 'px',
  top:    viewerPos.value.y + 'px',
  width:  VW + 'px',
  height: VH + 'px',
}))

// ── Mobile double-tap detection ──
const tapState = ref({ index: -1, time: 0 })

const handleTap = (i, event) => {
  event.preventDefault() // stop the synthetic click from also firing
  const now = Date.now()
  if (tapState.value.index === i && now - tapState.value.time < 350) {
    // Double-tap: open the item
    tapState.value = { index: -1, time: 0 }
    const item = currentItems.value[i]
    if (item?.type === 'folder') openFolder(item)
    else if (item?.type === 'pdf') openPdf(item)
    else openViewer(i)
  } else {
    // Single tap: select and show details
    tapState.value = { index: i, time: now }
    selectedIndex.value = i
  }
}

const openFolder = (folder) => {
  currentFolder.value = folder
  selectedIndex.value = null
  viewerOpen.value    = false
}

const navigateBack = () => {
  if (currentFolder.value) {
    currentFolder.value = null
    selectedIndex.value = null
    viewerOpen.value    = false
  }
}

const handleItemClick = (i) => {
  selectedIndex.value = i
}

const openViewer = (itemsIndex) => {
  const item = currentItems.value[itemsIndex]
  if (!item || item.type === 'folder') return
  const vIdx = viewableItems.value.indexOf(item)
  if (vIdx < 0) return
  viewerIndex.value    = vIdx
  viewerRotation.value = 0
  viewerZoom.value     = 1
  viewerPan.value      = { x: 0, y: 0 }
  viewerPos.value = {
    x: Math.max(20, (window.innerWidth  - VW) / 2 + 30),
    y: Math.max(20, (window.innerHeight - VH) / 2 - 20),
  }
  viewerOpen.value = true
}

const startViewerDrag = (e) => {
  viewerDragging.value = true
  viewerDragOff.value  = { x: e.clientX - viewerPos.value.x, y: e.clientY - viewerPos.value.y }
}

// ── Shared mouse handlers ──
const onMouseMove = (e) => {
  if (isDragging.value) {
    pos.value = { x: Math.max(0, e.clientX - dragOffset.value.x), y: Math.max(0, e.clientY - dragOffset.value.y) }
  }
  if (viewerDragging.value) {
    viewerPos.value = { x: Math.max(0, e.clientX - viewerDragOff.value.x), y: Math.max(0, e.clientY - viewerDragOff.value.y) }
  }
  if (isPanning.value) {
    viewerPan.value = {
      x: viewerPan.value.x + (e.clientX - panStart.value.x),
      y: viewerPan.value.y + (e.clientY - panStart.value.y),
    }
    panStart.value = { x: e.clientX, y: e.clientY }
  }
}
const onMouseUp = () => { isDragging.value = false; viewerDragging.value = false; isPanning.value = false }

onMounted(() => {
  window.addEventListener('mousemove', onMouseMove)
  window.addEventListener('mouseup', onMouseUp)
})
onUnmounted(() => {
  window.removeEventListener('mousemove', onMouseMove)
  window.removeEventListener('mouseup', onMouseUp)
})
</script>

<style scoped>
/* ── Overlay ── */
.xp-overlay {
  position: fixed;
  inset: 0;
  z-index: 99998;
  background: rgba(0, 0, 0, 0.22);
}

/* ── Window shell ── */
.xp-window {
  position: absolute;
  display: flex;
  flex-direction: column;
  font-family: 'Tahoma', 'Arial', sans-serif;
  font-size: 11px;
  user-select: none;
  border: 3px solid #164C95;
  border-top-left-radius: 8px;
  border-top-right-radius: 8px;
  outline: 1px solid #0d3068;
  box-shadow:
    inset 0 0 0 1px rgba(255, 255, 255, 0.3),
    5px 5px 20px rgba(0, 0, 0, 0.5),
    0 0 0 1px rgba(0, 0, 0, 0.3);
  overflow: hidden;
}

/* ── Title bar ── */
.xp-titlebar {
  background: linear-gradient(180deg,
    #2e7fd9 0%,
    #1a68c8 8%,
    #164C95 40%,
    #113d7a 100%
  );
  border-top: 1px solid #5aa0e8;
  padding: 3px 4px 3px 8px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  cursor: default;
  min-height: 30px;
  position: relative;
}

.xp-title-left {
  display: flex;
  align-items: center;
  gap: 6px;
}

.xp-title-text {
  color: white;
  font-weight: 700;
  font-family: 'Poppins', sans-serif;
  font-size: 12px;
  letter-spacing: 0.06em;
  text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.5);
}

/* ── Window buttons ── */
.xp-btns { display: flex; gap: 2px; align-items: center; }

.xp-btn {
  width: 21px;
  height: 21px;
  border-radius: 3px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid rgba(0, 0, 0, 0.4);
  box-shadow: inset 0 1px 0 rgba(255,255,255,0.4), inset 0 -1px 0 rgba(0,0,0,0.2);
  padding: 0;
  transition: filter 0.1s;
}

.xp-btn-min, .xp-btn-max {
  background: linear-gradient(180deg, #6aaaf0 0%, #3a80d8 45%, #2468c4 55%, #1858b8 100%);
}

/* Close: light blue #6D9FE7 */
.xp-btn-close {
  background: linear-gradient(180deg, #a8c8f8 0%, #6D9FE7 45%, #5888d8 55%, #4070c8 100%);
  margin-left: 2px;
}

.xp-btn:hover  { filter: brightness(1.2); }
.xp-btn:active { filter: brightness(0.85); box-shadow: inset 0 2px 3px rgba(0,0,0,0.3); }

/* ── Menu bar ── */
.xp-menubar {
  background: #eef4fd;
  border-bottom: 1px solid #b8d0ee;
  padding: 1px 4px;
  display: flex;
  gap: 0;
}

.xp-menu-item {
  padding: 2px 8px;
  font-family: 'Montserrat Alternates', sans-serif;
  font-size: 11px;
  color: #000;
  cursor: default;
  border-radius: 2px;
}
.xp-menu-item:hover { background: #164C95; color: white; }

/* ── Toolbar ── */
.xp-toolbar {
  background: linear-gradient(180deg, #f5f9ff 0%, #e8f0fc 100%);
  border-bottom: 1px solid #b8d0ee;
  padding: 2px 6px;
  display: flex;
  align-items: center;
  gap: 2px;
}

.xp-tool-btn {
  display: flex;
  align-items: center;
  gap: 3px;
  padding: 2px 6px;
  background: transparent;
  border: 1px solid transparent;
  border-radius: 2px;
  cursor: default;
  font-size: 11px;
  color: #000;
  height: 24px;
}
.xp-tool-btn:hover { border-color: #164C95; background: rgba(22, 76, 149, 0.08); }
.xp-tool-label { font-family: 'Montserrat Alternates', sans-serif; font-size: 11px; }

.xp-toolbar-sep {
  width: 1px;
  height: 22px;
  background: linear-gradient(180deg, transparent, #b8d0ee, transparent);
  margin: 0 4px;
}

/* ── Address bar ── */
.xp-addrbar {
  background: #eef4fd;
  border-bottom: 2px solid #b8d0ee;
  padding: 2px 6px;
  display: flex;
  align-items: center;
  gap: 4px;
  height: 26px;
}

.xp-addr-label {
  font-family: 'Montserrat Alternates', sans-serif;
  font-size: 11px;
  color: #333;
  padding: 0 6px;
  white-space: nowrap;
}

.xp-addr-input {
  flex: 1;
  background: white;
  border: 1px solid #7b9ac8;
  border-top-color: #4a6a9a;
  border-left-color: #4a6a9a;
  padding: 1px 6px;
  font-family: 'Montserrat Alternates', sans-serif;
  font-size: 11px;
  display: flex;
  align-items: center;
  gap: 5px;
  height: 18px;
  color: #000;
  overflow: hidden;
  white-space: nowrap;
}

.xp-go-btn {
  background: linear-gradient(180deg, #f5f9ff 0%, #ddeafc 100%);
  border: 1px solid #8aaad0;
  border-radius: 2px;
  padding: 1px 8px;
  font-family: 'Montserrat Alternates', sans-serif;
  font-size: 11px;
  cursor: default;
  height: 20px;
}
.xp-go-btn:hover  { background: linear-gradient(180deg, #ffffff 0%, #e8f2ff 100%); }
.xp-go-btn:active { background: #d0e4f8; }

/* ── Main body ── */
.xp-body { display: flex; flex: 1; min-height: 0; overflow: hidden; }

/* ── Sidebar ── */
.xp-sidebar {
  width: 160px;
  flex-shrink: 0;
  min-height: 0;
  background: linear-gradient(180deg, #d8eafc 0%, #c8ddf8 100%);
  border-right: 1px solid #8aaad0;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
  gap: 1px;
  padding-bottom: 8px;
}

.xp-sidebar-section {
  background: rgba(255,255,255,0.45);
  margin: 3px 4px 0;
  border-radius: 4px;
  overflow: hidden;
}

.xp-sidebar-header {
  background: linear-gradient(180deg, #2a70c8 0%, #164C95 100%);
  color: white;
  font-family: 'Poppins', sans-serif;
  font-weight: 700;
  font-size: 10px;
  padding: 4px 8px;
  letter-spacing: 0.04em;
}

.xp-sidebar-links {
  padding: 5px 6px;
  display: flex;
  flex-direction: column;
  gap: 3px;
}

.xp-sidebar-link {
  display: flex;
  align-items: center;
  gap: 5px;
  font-family: 'Montserrat Alternates', sans-serif;
  font-size: 10px;
  color: #164C95;
  text-decoration: underline;
  cursor: pointer;
  padding: 2px 3px;
  border-radius: 2px;
  border: 1px solid transparent;
}
.xp-sidebar-link:hover { background: rgba(22,76,149,0.1); border-color: #b0cce8; }
.xp-sidebar-link-active {
  background: rgba(22,76,149,0.15) !important;
  border-color: #164C95 !important;
  font-weight: 700;
  text-decoration: none;
  color: #0d3068;
}

/* Details panel */
.xp-sidebar-detail {
  padding: 4px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 3px;
}

.xp-detail-thumb {
  width: 60px;
  height: 60px;
  border: 1px solid #b8d0ee;
  overflow: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
  background: white;
}
.xp-detail-thumb img { max-width: 100%; max-height: 100%; object-fit: cover; }

.xp-detail-name {
  font-family: 'Poppins', sans-serif;
  font-weight: 700;
  font-size: 10px;
  color: #164C95;
  text-align: center;
  word-break: break-word;
  margin: 0;
}

.xp-detail-type {
  font-family: 'Montserrat Alternates', sans-serif;
  font-size: 10px;
  color: #6D9FE7;
  margin: 0;
}

.xp-detail-desc {
  font-family: 'Montserrat Alternates', sans-serif;
  font-size: 10px;
  color: #333;
  margin: 2px 0 0;
  text-align: center;
  line-height: 1.4;
  font-style: italic;
  max-height: 72px;
  overflow-y: auto;
  overflow-x: hidden;
  width: 100%;
}

/* ── Content area ── */
.xp-content {
  flex: 1;
  background: white;
  border-left: 1px solid #d0e4f8;
  overflow-y: auto;
  padding: 10px;
}

.xp-coming-soon {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100%;
  min-height: 200px;
  text-align: center;
  opacity: 0.85;
}

.xp-content::-webkit-scrollbar { width: 16px; }
.xp-content::-webkit-scrollbar-track { background: #eef4fd; border-left: 1px solid #b8d0ee; }
.xp-content::-webkit-scrollbar-thumb {
  background: linear-gradient(90deg, #c8d8f0, #b0c8e8);
  border: 1px solid #8aaad0;
  border-radius: 2px;
}

/* ── Image grid ── */
.xp-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(110px, 1fr));
  gap: 4px;
  align-content: start;
}

.xp-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 3px;
  padding: 5px;
  border-radius: 2px;
  cursor: default;
  border: 1px solid transparent;
}
.xp-item:hover { background: #ddeeff; border-color: #6D9FE7; }

.xp-item-selected { background: #164C95 !important; border-color: #164C95 !important; }
.xp-item-selected .xp-filename { color: white !important; }

.xp-thumb {
  width: 96px;
  height: 96px;
  border: 1px solid #c8daf0;
  overflow: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #f5f9ff;
  border-radius: 1px;
}
.xp-thumb img { max-width: 100%; max-height: 100%; object-fit: cover; display: block; }

.xp-filename {
  font-family: 'Montserrat Alternates', sans-serif;
  font-size: 11px;
  color: #164C95;
  text-align: center;
  max-width: 100px;
  word-break: break-word;
  line-height: 1.2;
}

/* ── Empty state ── */
.xp-empty {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100%;
  min-height: 320px;
  gap: 8px;
}

.xp-empty-text {
  font-family: 'Poppins', sans-serif;
  font-weight: 700;
  font-size: 13px;
  color: #164C95;
  margin: 0;
}

.xp-empty-hint {
  font-family: 'Montserrat Alternates', sans-serif;
  font-size: 11px;
  color: #6D9FE7;
  margin: 0;
}

/* ── Status bar ── */
.xp-statusbar {
  background: #eef4fd;
  border-top: 2px solid #b8d0ee;
  display: flex;
  align-items: center;
  justify-content: space-between;
  height: 22px;
  padding: 0 2px;
}

.xp-status-seg {
  flex: 1;
  border-right: 1px solid #b8d0ee;
  padding: 0 6px;
  font-family: 'Montserrat Alternates', sans-serif;
  font-size: 11px;
  color: #333;
  height: 100%;
  display: flex;
  align-items: center;
}

.xp-status-grip { padding: 0 2px; cursor: se-resize; }

/* ── Folder icon in grid ── */
.xp-folder-thumb {
  display: flex;
  align-items: center;
  justify-content: center;
  background: #f5f9ff;
}

/* ── Folder icon in details panel ── */
.xp-detail-folder-thumb {
  display: flex;
  align-items: center;
  justify-content: center;
  background: transparent;
  border: none;
}

/* ── Live site link in details panel ── */
.xp-detail-link {
  display: inline-block;
  margin-top: 4px;
  padding: 2px 8px;
  background: linear-gradient(180deg, #3a80d8 0%, #164C95 100%);
  color: white;
  font-family: 'Montserrat Alternates', sans-serif;
  font-size: 10px;
  font-weight: 700;
  border-radius: 3px;
  text-decoration: none;
  border: 1px solid #0d3068;
  cursor: pointer;
}
.xp-detail-link:hover { filter: brightness(1.15); }

/* ── Picture Viewer window ── */
.xp-viewer-window {
  position: fixed;
  z-index: 99999;
  display: flex;
  flex-direction: column;
  font-family: 'Tahoma', 'Arial', sans-serif;
  font-size: 11px;
  user-select: none;
  border: 3px solid #164C95;
  border-top-left-radius: 8px;
  border-top-right-radius: 8px;
  outline: 1px solid #0d3068;
  box-shadow:
    inset 0 0 0 1px rgba(255,255,255,0.3),
    6px 6px 22px rgba(0,0,0,0.6),
    0 0 0 1px rgba(0,0,0,0.35);
  overflow: hidden;
  background: #1c1c1c;
}

.xp-viewer-titlebar { cursor: default; }

.xp-viewer-image-area {
  flex: 1;
  background: #1c1c1c;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  padding: 12px;
}

.xp-viewer-img {
  max-width: 100%;
  max-height: 440px;
  object-fit: contain;
  transform-origin: center center;
  pointer-events: none;
}

.xp-viewer-toolbar {
  background: linear-gradient(180deg, #f5f9ff 0%, #e8f0fc 100%);
  border-top: 2px solid #b8d0ee;
  padding: 4px 8px;
  display: flex;
  align-items: center;
  gap: 2px;
  height: 36px;
}

.xp-viewer-btn {
  width: 28px;
  height: 26px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(180deg, #f8f5ff 0%, #e4edf8 100%);
  border: 1px solid #8aaad0;
  border-radius: 2px;
  cursor: pointer;
  padding: 0;
}
.xp-viewer-btn:hover:not(:disabled)  { background: linear-gradient(180deg, #fff 0%, #d8eaff 100%); border-color: #164C95; }
.xp-viewer-btn:active:not(:disabled) { background: #c8dcf8; }
.xp-viewer-btn:disabled { opacity: 0.4; cursor: default; }

.xp-viewer-sep {
  width: 1px;
  height: 22px;
  background: linear-gradient(180deg, transparent, #b8d0ee, transparent);
  margin: 0 4px;
}

.xp-viewer-counter {
  margin-left: auto;
  font-family: 'Montserrat Alternates', sans-serif;
  font-size: 11px;
  color: #333;
  padding: 0 4px;
}

/* ── Mobile ── */
@media (max-width: 860px) {
  .xp-window {
    width: calc(100vw - 16px) !important;
    left: 8px !important;
    top: 8px !important;
    min-height: calc(100vh - 16px);
  }
  .xp-sidebar { display: none; }
  .xp-grid { grid-template-columns: repeat(auto-fill, minmax(90px, 1fr)); }
  .xp-viewer-window { width: calc(100vw - 16px) !important; left: 8px !important; top: 8px !important; }
}

/* ── Mobile details bottom panel ── */
.xp-mobile-details {
  display: none;
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 100000;
  background: white;
  border-top: 2px solid #164C95;
  box-shadow: 0 -4px 24px rgba(22, 76, 149, 0.18);
  max-height: 55vh;
  overflow-y: auto;
}

.xp-mobile-details-bar {
  background: linear-gradient(180deg, #2a70c8 0%, #164C95 100%);
  color: white;
  font-family: 'Poppins', sans-serif;
  font-weight: 700;
  font-size: 12px;
  padding: 6px 10px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  letter-spacing: 0.04em;
  position: sticky;
  top: 0;
}

.xp-mobile-details-title { flex: 1; }

.xp-mobile-details-close {
  background: transparent;
  border: none;
  color: white;
  font-size: 16px;
  line-height: 1;
  cursor: pointer;
  padding: 0 4px;
}

.xp-mobile-details-body {
  padding: 10px 14px 16px;
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.xp-mobile-detail-name {
  font-family: 'Poppins', sans-serif;
  font-weight: 700;
  font-size: 13px;
  color: #164C95;
  margin: 0;
  word-break: break-word;
}

.xp-mobile-detail-type {
  font-family: 'Montserrat Alternates', sans-serif;
  font-size: 11px;
  color: #6D9FE7;
  margin: 0;
}

.xp-mobile-detail-desc {
  font-family: 'Montserrat Alternates', sans-serif;
  font-size: 12px;
  color: #333;
  line-height: 1.5;
  margin: 4px 0 0;
  font-style: italic;
}

/* Slide-up transition */
.xp-slide-up-enter-active { transition: transform 0.25s ease, opacity 0.25s ease; }
.xp-slide-up-leave-active { transition: transform 0.2s ease, opacity 0.2s ease; }
.xp-slide-up-enter-from  { transform: translateY(100%); opacity: 0; }
.xp-slide-up-leave-to    { transform: translateY(100%); opacity: 0; }

@media (max-width: 860px) {
  .xp-mobile-details { display: block; }
}
</style>
