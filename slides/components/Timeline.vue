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
  background-image: repeating-linear-gradient(
    to right,
    rgb(var(--ink)) 0,
    rgb(var(--ink)) 8px,
    transparent 8px,
    transparent 14px
  );
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
  background: rgb(var(--primary));
  border: 2px solid rgb(var(--ink));
  box-shadow: 2px 2px 0 rgb(var(--secondary) / 70%);
  z-index: 1;
  flex-shrink: 0;
}

.tl-year {
  margin-top: 0.6rem;
  font-family: 'Martian Mono', monospace;
  font-weight: 700;
  font-size: 1.1rem;
  color: rgb(var(--primary));
  font-variant-numeric: tabular-nums;
  height: 1.8rem;
  display: flex;
  align-items: center;
}

.tl-desc {
  margin-top: 0.6rem;
  font-size: 0.85rem;
  color: rgb(var(--ink));
  line-height: 1.4;
  background: rgb(var(--surface));
  border: 2.5px solid rgb(var(--ink));
  border-radius: 0.5rem;
  overflow: hidden;
  width: 90%;
  box-shadow: 4px 3px 0 rgb(var(--primary) / 55%);
}

.tl-card-title {
  font-weight: 700;
  font-size: 0.9rem;
  padding: 0.4rem 0.6rem;
  background: rgb(var(--primary) / 18%);
  border-bottom: 2px solid rgb(var(--ink));
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
  background: rgb(var(--accent));
  width: 18px;
  height: 18px;
  box-shadow: 3px 3px 0 rgb(var(--secondary) / 70%);
  margin-top: -2px;
}

.tl-item.highlight .tl-year {
  color: rgb(var(--accent-text));
  font-size: 1.2rem;
}

.tl-item.highlight .tl-desc {
  border-color: rgb(var(--accent-text));
  box-shadow: 4px 3px 0 rgb(var(--accent) / 60%);
  font-weight: 600;
}

.tl-item.highlight .tl-card-title {
  background: rgb(var(--accent) / 22%);
  border-bottom-color: rgb(var(--accent-text));
}
</style>
