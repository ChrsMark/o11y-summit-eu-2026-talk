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
  background: linear-gradient(90deg, rgb(var(--primary)) 0%, rgb(var(--secondary)) 45%, rgb(var(--accent)) 100%);
  border-radius: 2px;
  box-shadow: 0 0 12px rgb(var(--accent) / 35%);
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
  border: 2px solid #0b1130;
  box-shadow: 0 0 10px 2px rgb(var(--primary) / 55%);
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
  color: rgb(var(--code-text));
  line-height: 1.4;
  background: rgb(255 255 255 / 6%);
  backdrop-filter: blur(14px) saturate(140%);
  border: 1px solid rgb(255 255 255 / 14%);
  border-radius: var(--radius-md);
  overflow: hidden;
  width: 90%;
  box-shadow: inset 0 1px 0 rgb(255 255 255 / 18%), 0 10px 26px rgb(0 0 0 / 30%);
}

.tl-card-title {
  font-weight: 700;
  font-size: 0.9rem;
  padding: 0.4rem 0.6rem;
  background: linear-gradient(135deg, rgb(var(--primary) / 32%), rgb(var(--primary) / 14%));
  border-bottom: 1px solid rgb(255 255 255 / 14%);
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
  background: rgb(var(--success));
  width: 18px;
  height: 18px;
  box-shadow: 0 0 16px 4px rgb(var(--success) / 55%);
  margin-top: -2px;
}

.tl-item.highlight .tl-year {
  color: rgb(var(--success));
  font-size: 1.2rem;
}

.tl-item.highlight .tl-desc {
  color: #f8f8f2;
  font-weight: 600;
  border-color: rgb(var(--success) / 45%);
  box-shadow: inset 0 1px 0 rgb(255 255 255 / 18%), 0 0 30px rgb(var(--success) / 25%), 0 10px 26px rgb(0 0 0 / 30%);
}




</style>
