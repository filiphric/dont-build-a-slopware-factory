<script setup lang="ts">
import { computed } from 'vue'
import { useIsSlideActive, useSlideContext } from '@slidev/client'

const active = useIsSlideActive()
const { $renderContext, $slidev } = useSlideContext()
const showEntry = computed(() => active.value || ['overview', 'previewNext', 'print'].includes($renderContext.value))
const staticView = computed(() => ['overview', 'previewNext', 'print'].includes($renderContext.value))
</script>

<template>
  <div class="slidev-layout editorial verification-layout" :class="{ entered: showEntry, 'is-static': staticView }">
    <main class="verification-content">
      <div class="verification-copy"><slot /></div>
      <figure class="verification-art"><slot name="image" /></figure>
    </main>
    <footer class="editorial-footer">
      <span class="editorial-wordmark">filiphric.com</span>
      <span>{{ $slidev.configs.event || $slidev.configs.title }}</span>
    </footer>
  </div>
</template>

<style scoped>
.verification-content {
  display: grid;
  grid-template-columns: 1.55fr 1fr;
  grid-template-rows: auto 1fr;
  column-gap: 24px;
  row-gap: 28px;
  height: 420px;
}
.verification-copy { display: contents; }
.verification-copy :deep(h1) { grid-column: 1 / -1; margin: 0; font-size: 46px; opacity: 0; }
.verification-copy :deep(ul) { grid-column: 1; grid-row: 2; align-self: center; padding-left: 1em; }
.verification-copy :deep(li) { margin: 0 0 22px; padding-left: 5px; font-size: 23px; line-height: 1.3; }
.verification-copy :deep(li:last-child) { margin-bottom: 0; }
.verification-copy :deep(li:not(.slidev-vclick-hidden)) { animation: verification-bullet 300ms steps(1, end) both; }
.verification-art { grid-column: 2; grid-row: 2; align-self: center; margin: 0; opacity: 0; }
.verification-art :deep(p) { margin: 0; }
.verification-art :deep(img) { display: block; width: 100%; height: 325px; object-fit: contain; }
.entered .verification-copy :deep(h1) { animation: verification-entry 360ms steps(1, end) both; }
.entered .verification-art { animation: verification-entry 420ms steps(1, end) 120ms both; }
@keyframes verification-entry {
  0% { opacity: 0; transform: translateY(16px) rotate(-2deg) scale(.96); }
  1% { opacity: 1; transform: translateY(16px) rotate(-2deg) scale(.96); }
  34% { opacity: 1; transform: translateY(-3px) rotate(1deg) scale(1.015); }
  67% { opacity: 1; transform: translateY(2px) rotate(-.4deg); }
  100% { opacity: 1; transform: none; }
}
@keyframes verification-bullet {
  0% { opacity: 0; transform: translateX(-10px); }
  1% { opacity: 1; transform: translateX(-10px); }
  50% { opacity: 1; transform: translateX(2px); }
  100% { opacity: 1; transform: none; }
}
.is-static .verification-copy :deep(h1), .is-static .verification-art { opacity: 1; animation: none; }
.is-static .verification-copy :deep(li) { animation: none; }
@media (prefers-reduced-motion: reduce) {
  .entered .verification-copy :deep(h1), .entered .verification-art { opacity: 1; animation: none; }
  .verification-copy :deep(li) { animation: none !important; }
}
</style>
