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
  top: calc(0.5rem + 7px - 0.5px);
  left: 0;
  right: 0;
  height: 1px;
  background: var(--slidev-theme-primary);
}

.timeline-items {
  display: flex;
  justify-content: space-between;
  position: relative;
}

.tl-item {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  flex: 1;
  min-width: 0;
  padding-right: 0.75rem;
}

.tl-dot {
  width: 10px;
  height: 10px;
  border-radius: 0;
  background: var(--slidev-theme-primary);
  border: none;
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
  color: var(--comment);
  line-height: 1.4;
  border-top: 2px solid var(--slidev-theme-primary);
  width: 90%;
  text-align: left;
}

.tl-card-title {
  font-weight: 700;
  font-size: 0.9rem;
  padding: 0.5rem 0 0.2rem;
  text-align: left;
  color: var(--slidev-theme-primary);
}

.tl-body {
  margin: 0 !important;
  padding: 0 !important;
  font-size: 0.78rem !important;
  line-height: 1.45 !important;
  text-align: left;
  color: var(--comment);
}

.tl-item.highlight .tl-dot {
  background: rgb(var(--accent));
  width: 10px;
  height: 10px;
  margin-top: 0;
}

.tl-item.highlight .tl-year {
  color: rgb(var(--accent));
  font-size: 1.2rem;
}

.tl-item.highlight .tl-desc {
  border-top: 2px solid rgb(var(--accent));
}

.tl-item.highlight .tl-card-title {
  color: rgb(var(--accent));
  font-weight: 700;
}
</style>
