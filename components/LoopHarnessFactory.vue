<script setup lang="ts">
import { computed, useId } from 'vue'
import { useSlideContext } from '@slidev/client'

const { $clicks, $renderContext } = useSlideContext()
const id = `lhf-${useId().replace(/[^a-zA-Z0-9-]/g, '')}`
const complete = computed(() => ['overview', 'previewNext', 'print'].includes($renderContext.value))
const shown = (step: number) => complete.value || $clicks.value >= step
const sections = [
  { name: 'loop', step: 1, clip: 'M 0 0 H 560 V 330 H 550 V 465 H 575 V 756 H 0 Z' },
  // The incoming connector belongs to the section it introduces.
  { name: 'harness', step: 2, clip: 'M 560 0 H 1300 V 330 H 1210 V 465 H 1300 V 756 H 575 V 465 H 550 V 330 H 560 Z' },
  { name: 'factory', step: 3, clip: 'M 1300 0 H 2079 V 756 H 1300 V 465 H 1210 V 330 H 1300 Z' },
]
const layers = [
  { name: 'teal', file: 'teal.png', delay: 0, crop: undefined },
  { name: 'yellow', file: 'yellow.png', delay: 140, crop: undefined },
  { name: 'heading', file: 'foreground-v2.png', delay: 240, crop: 'heading' },
  { name: 'diagram', file: 'foreground-v2.png', delay: 360, crop: 'diagram' },
  { name: 'caption', file: 'foreground-v2.png', delay: 540, crop: 'caption' },
]
</script>

<template>
  <svg class="factory-graph" :class="{ 'is-static': complete }" viewBox="0 0 2079 1170" role="img"
    aria-label="The loop: one agent gathers context, acts, verifies, and repeats. The harness wraps a loop with a sandbox, tools, memory, and gates defining done. The factory runs three harnesses through one review and merge gate into production, with you overseeing the outer loop.">
    <defs>
      <clipPath v-for="section in sections" :id="`${id}-${section.name}`" :key="section.name">
        <path :d="section.clip" />
      </clipPath>
      <clipPath :id="`${id}-heading`"><rect width="2079" height="165" /></clipPath>
      <clipPath :id="`${id}-diagram`"><rect y="165" width="2079" height="465" /></clipPath>
      <clipPath :id="`${id}-caption`"><rect y="630" width="2079" height="126" /></clipPath>
    </defs>
    <g transform="translate(0 207)">
      <g v-for="section in sections" :key="section.name" v-click="section.step" :data-section="section.name"
        :aria-hidden="!shown(section.step)" :clip-path="`url(#${id}-${section.name})`">
        <g v-for="layer in layers" :key="layer.name" class="cutout"
          :class="[layer.name, { visible: shown(section.step) }]"
          :style="{ animationDelay: shown(section.step) ? `${layer.delay}ms` : '0ms' }">
          <g :clip-path="layer.crop ? `url(#${id}-${layer.crop})` : undefined">
            <image :href="`/images/loop-harness-factory/${layer.file}`" width="2079" height="756" />
          </g>
        </g>
      </g>
    </g>
  </svg>
</template>

<style scoped>
.factory-graph { display: block; width: 100%; height: 100%; }
.cutout {
  --start: translateY(22px) rotate(-2deg) scale(.96);
  --over: translateY(-4px) rotate(1deg) scale(1.015);
  opacity: 0;
  transform-box: fill-box;
  transform-origin: center;
}
.cutout.visible { opacity: 1; animation: factory-cutout-in 360ms steps(1, end) both; }
.teal { --start: translate(-18px, 14px) rotate(-4deg); }
.yellow { --start: scale(.75) rotate(6deg); --over: scale(1.04) rotate(-2deg); }
.heading { --start: translateY(-14px) rotate(-3deg); }
.caption { --start: translateY(12px) rotate(2deg); }
@keyframes factory-cutout-in {
  0% { opacity: 0; transform: var(--start); }
  1% { opacity: 1; transform: var(--start); }
  34% { opacity: 1; transform: var(--over); }
  67% { opacity: 1; transform: translateY(2px) rotate(-.5deg); }
  100% { opacity: 1; transform: none; }
}
.is-static .cutout.visible { animation: none; transform: none; }
@media (prefers-reduced-motion: reduce) {
  .cutout { animation: none !important; transform: none; }
}
</style>
