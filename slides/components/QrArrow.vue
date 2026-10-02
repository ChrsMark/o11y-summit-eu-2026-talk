<script setup lang="ts">
import { ref, onMounted, computed, watch } from 'vue'
import QRCode from 'qrcode'
import { useNav } from '@slidev/client'

const props = defineProps<{
  slide?: string
}>()

const { currentPage } = useNav()

const qrLight = ref('')
const qrDark = ref('')

const baseUrl = 'https://stable-by-default-2026.pages.dev/'
const url = computed(() => {
  const page = props.slide ?? String(currentPage.value)
  return `${baseUrl}${page}`
})
const opts = { width: 240, margin: 1 }

async function generateQR() {
  qrLight.value = await QRCode.toDataURL(url.value, {
    ...opts,
    color: { dark: '#000000', light: '#00000000' },
  })
  qrDark.value = await QRCode.toDataURL(url.value, {
    ...opts,
    color: { dark: '#e0e0e0', light: '#00000000' },
  })
}

onMounted(generateQR)
watch(url, generateQR)
</script>

<template>
  <div class="qr-arrow-container">
    <img v-if="qrLight" :src="qrLight" class="qr-code qr-for-light" alt="QR code to presentation" />
    <img v-if="qrDark" :src="qrDark" class="qr-code qr-for-dark" alt="QR code to presentation" />
    <div class="label-arrow">
      <span class="label-text">Slides link!</span>
      <svg class="arrow-svg" viewBox="0 0 70 50" fill="none" xmlns="http://www.w3.org/2000/svg">
        <path
          d="M 60,2 C 65,20 45,35 12,40"
          stroke="currentColor"
          stroke-width="2.5"
          stroke-linecap="round"
          fill="none"
        />
        <polygon
          points="12,40 23.5,33 19,44.6"
          fill="currentColor"
        />
      </svg>
    </div>
  </div>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Caveat:wght@700&display=swap');

.qr-arrow-container {
  position: absolute;
  bottom: 16px;
  left: 24px;
  z-index: 10;
  display: flex;
  flex-direction: row;
  align-items: flex-end;
  gap: 4px;
  color: #e0e0e0;
}

html:not(.dark) .qr-arrow-container {
  color: #000000;
}

.qr-code {
  width: 100px;
  height: 100px;
}

/* Dark mode: show dark QR (light colored), hide light QR */
.qr-for-light {
  display: none;
}

.qr-for-dark {
  display: block;
}

/* Light mode: show light QR (dark colored), hide dark QR */
html:not(.dark) .qr-for-light {
  display: block;
}

html:not(.dark) .qr-for-dark {
  display: none;
}

.label-arrow {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  margin-bottom: 10px;
}

.label-text {
  font-family: 'Caveat', cursive;
  font-weight: 700;
  font-size: 22px;
  color: currentColor;
  white-space: nowrap;
  transform: rotate(-4deg);
}

.arrow-svg {
  width: 55px;
  height: 40px;
  margin-top: -2px;
  pointer-events: none;
}
</style>
