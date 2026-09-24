<script setup lang="ts">
interface TimelineItem {
  year: string
  desc: string
  highlight?: boolean
}

defineProps<{
  items: TimelineItem[]
}>()
</script>

<template>
  <div class="timeline-wrapper">
    <div class="timeline">
      <div class="timeline-track"></div>
      <div class="timeline-items">
        <div
          v-for="(item, i) in items"
          :key="i"
          v-click
          class="tl-item"
          :class="{ highlight: item.highlight }"
        >
          <div class="tl-dot"></div>
          <div class="tl-year" v-html="item.year"></div>
          <div class="tl-desc" v-html="item.desc"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
.timeline-wrapper {
  flex: 1;
  display: flex;
  align-items: center;
  width: 100%;
  height: 100%;
}

.timeline {
  position: relative;
  width: 92%;
  margin: 0 auto;
  padding-top: 0.5rem;
}

.timeline-track {
  position: absolute;
  top: calc(0.5rem + 7px - 2px);
  left: 0;
  right: 0;
  height: 4px;
  background: #151515;
  border-radius: 0;
}

.timeline-items {
  display: flex;
  justify-content: space-between;
  position: relative;
}

.tl-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  flex: 1;
  min-width: 0;
}

.tl-dot {
  width: 14px;
  height: 14px;
  border-radius: 50%;
  background: #151515;
  border: 2px solid #151515;
  z-index: 1;
  flex-shrink: 0;
}

.tl-year {
  margin-top: 0.6rem;
  font-weight: 700;
  font-size: 1.1rem;
  color: #151515;
  font-variant-numeric: tabular-nums;
  height: 1.8rem;
  display: flex;
  align-items: center;
}

.tl-desc {
  margin-top: 0.6rem;
  font-size: 0.85rem;
  color: #151515;
  line-height: 1.4;
  background: rgb(var(--surface));
  border: 3px solid #151515;
  border-radius: 0;
  overflow: hidden;
  width: 90%;
}

/* Cycle the card-title band through the three primary Bauhaus colors */
.tl-item:nth-child(3n+1) .tl-card-title {
  background: rgb(var(--secondary));
  color: rgb(var(--surface));
}

.tl-item:nth-child(3n+2) .tl-card-title {
  background: rgb(var(--primary));
  color: rgb(var(--surface));
}

.tl-item:nth-child(3n+3) .tl-card-title {
  background: rgb(var(--accent));
  color: #151515;
}

.tl-card-title {
  font-weight: 700;
  font-size: 0.9rem;
  padding: 0.4rem 0.6rem;
  border-bottom: 3px solid #151515;
  text-align: center;
}

.tl-body {
  margin: 0 !important;
  padding: 0.45rem 0.6rem 0.5rem !important;
  font-size: 0.78rem !important;
  line-height: 1.45 !important;
  text-align: center;
  color: inherit;
}

.tl-item.highlight .tl-dot {
  background: rgb(var(--success));
  border-color: #151515;
  width: 18px;
  height: 18px;
  margin-top: -2px;
}

.tl-item.highlight .tl-year {
  color: rgb(var(--success));
  font-size: 1.2rem;
}

.tl-item.highlight .tl-desc {
  border-width: 4px;
  font-weight: 600;
}
</style>
