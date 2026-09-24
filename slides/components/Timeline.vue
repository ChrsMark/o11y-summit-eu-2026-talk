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
  top: calc(0.5rem + 7px - 1.5px);
  left: 0;
  right: 0;
  height: 3px;
  background: linear-gradient(90deg, rgb(var(--neutral)) 0%, rgb(var(--primary)) 100%);
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
  width: 16px;
  height: 16px;
  border-radius: 0;
  background: rgb(var(--primary));
  border: 3px solid rgb(var(--page));
  z-index: 1;
  flex-shrink: 0;
}

.tl-year {
  margin-top: 0.6rem;
  font-weight: 700;
  font-size: 1.1rem;
  font-family: 'JetBrains Mono', monospace;
  color: rgb(var(--primary));
  font-variant-numeric: tabular-nums;
  height: 1.8rem;
  display: flex;
  align-items: center;
}

.tl-desc {
  margin-top: 0.6rem;
  font-size: 0.85rem;
  color: rgb(var(--ink) / 80%);
  line-height: 1.4;
  background: rgb(var(--surface));
  border: 3px solid rgb(var(--ink));
  border-radius: 0;
  overflow: hidden;
  width: 90%;
  box-shadow: 6px 6px 0 rgb(var(--secondary));
}

.tl-card-title {
  font-weight: 700;
  font-size: 0.9rem;
  padding: 0.4rem 0.6rem;
  background: rgb(var(--surface));
  border-bottom: 3px solid rgb(var(--ink));
  text-align: center;
  color: rgb(var(--ink));
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
  width: 20px;
  height: 20px;
  box-shadow: 3px 3px 0 rgb(var(--success) / 70%);
  margin-top: -2px;
}

.tl-item.highlight .tl-year {
  color: rgb(var(--success));
  font-size: 1.2rem;
}

.tl-item.highlight .tl-desc {
  box-shadow: 6px 6px 0 rgb(var(--primary));
}

.tl-item.highlight .tl-card-title {
  background: rgb(var(--success));
  color: rgb(var(--page));
  border-bottom: 3px solid rgb(var(--page));
}

.tl-item.highlight .tl-body {
  color: rgb(var(--ink));
  font-weight: 600;
}




</style>
