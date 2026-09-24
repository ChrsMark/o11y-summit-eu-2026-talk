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
  background: linear-gradient(135deg, rgb(var(--surface) / 50%), rgb(var(--surface-deep) / 45%));
  border: 1.5px solid rgb(var(--primary) / 20%);
  border-radius: 0.5rem;
  overflow: hidden;
  width: 90%;
  box-shadow: 0 4px 16px rgb(0 0 0 / 15%);
}

.tl-card-title {
  font-weight: 700;
  font-size: 0.9rem;
  padding: 0.4rem 0.6rem;
  background: linear-gradient(135deg, rgb(var(--primary) / 25%), rgb(var(--primary) / 10%));
  border-bottom: 1.5px solid rgb(var(--primary) / 15%);
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
  box-shadow: 0 0 8px rgb(var(--success) / 60%);
  margin-top: -2px;
}

.tl-item.highlight .tl-year {
  color: rgb(var(--success));
  font-size: 1.2rem;
}

.tl-item.highlight .tl-desc {
  color: #f8f8f2;
  font-weight: 600;
}




</style>
