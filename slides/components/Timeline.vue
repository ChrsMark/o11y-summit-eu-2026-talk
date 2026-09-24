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
  background: linear-gradient(90deg, #555 0%, var(--slidev-theme-primary) 100%);
  border-radius: 2px;
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
  background: var(--slidev-theme-primary);
  border: 2px solid #1e1e1e;
  z-index: 1;
  flex-shrink: 0;
}

.tl-year {
  margin-top: 0.6rem;
  font-weight: 700;
  font-size: 1.1rem;
  color: var(--slidev-theme-primary);
  font-variant-numeric: tabular-nums;
  height: 1.8rem;
  display: flex;
  align-items: center;
}

.tl-desc {
  margin-top: 0.6rem;
  font-size: 0.85rem;
  color: #ccc;
  line-height: 1.4;
  background: linear-gradient(135deg, rgba(54, 56, 85, 0.5), rgba(26, 28, 44, 0.45));
  border: 1.5px solid rgba(141, 141, 255, 0.2);
  border-radius: 0.5rem;
  overflow: hidden;
  width: 90%;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.15);
}

.tl-card-title {
  font-weight: 700;
  font-size: 0.9rem;
  padding: 0.4rem 0.6rem;
  background: linear-gradient(135deg, rgba(93, 93, 255, 0.25), rgba(141, 141, 255, 0.1));
  border-bottom: 1.5px solid rgba(141, 141, 255, 0.15);
  text-align: center;
  color: #f8f8f2;
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
  background: #50fa7b;
  width: 18px;
  height: 18px;
  box-shadow: 0 0 8px rgba(80, 250, 123, 0.6);
  margin-top: -2px;
}

.tl-item.highlight .tl-year {
  color: #50fa7b;
  font-size: 1.2rem;
}

.tl-item.highlight .tl-desc {
  color: #f8f8f2;
  font-weight: 600;
}

html:not(.dark) .tl-dot {
  border-color: #f0f0f0;
}

html:not(.dark) .tl-desc {
  color: #555;
  background: linear-gradient(135deg, rgba(240, 240, 255, 0.8), rgba(230, 230, 250, 0.6));
  border-color: rgba(99, 102, 241, 0.2);
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.06);
}

html:not(.dark) .tl-card-title {
  background: linear-gradient(135deg, rgba(99, 102, 241, 0.12), rgba(139, 92, 246, 0.06));
  border-bottom-color: rgba(99, 102, 241, 0.12);
  color: #1a1a1a;
}

html:not(.dark) .tl-item.highlight .tl-desc {
  color: #1a1a1a;
}
</style>
