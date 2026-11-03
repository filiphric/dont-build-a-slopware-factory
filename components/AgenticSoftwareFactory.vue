<script setup lang="ts">
import { computed, useId } from 'vue'
import { useSlideContext } from '@slidev/client'

const { $clicks, $renderContext } = useSlideContext()
const assetBase = import.meta.env.BASE_URL
const id = `asf-${useId().replace(/[^a-zA-Z0-9-]/g, '')}`
const complete = computed(() => ['overview', 'previewNext', 'print'].includes($renderContext.value))
const shown = (step: number) => complete.value || $clicks.value >= step
// Source tiles come from the registered 1672 × 941 imagegen plates.
const nodes = [
  { name: 'intent', step: 1, col: 0, row: 0, arrow: '' },
  { name: 'queue', step: 2, col: 1, row: 0, arrow: 'M 405 270 H 460' },
  { name: 'harness', step: 3, col: 2, row: 0, arrow: 'M 845 270 H 900' },
  { name: 'automated checks', step: 4, col: 3, row: 0, arrow: 'M 1285 270 H 1340' },
  { name: 'review', step: 5, col: 3, row: 1, arrow: 'M 1535 465 V 512' },
  { name: 'deploy', step: 6, col: 2, row: 1, arrow: 'M 1345 740 H 1288' },
  { name: 'monitor', step: 7, col: 1, row: 1, arrow: 'M 905 740 H 848' },
  { name: 'signals', step: 8, col: 0, row: 1, arrow: 'M 465 740 H 408' },
]
const layers = [
  { name: 'teal', file: 'teal.png', delay: 0, part: undefined },
  { name: 'yellow', file: 'yellow.png', delay: 140, part: undefined },
  { name: 'art', file: 'foreground.png', delay: 280, part: 'art' },
  { name: 'text', file: 'foreground.png', delay: 440, part: 'text' },
]
const sourceY = (row: number) => row === 0 ? 0 : 480
const sourceHeight = (row: number) => row === 0 ? 470 : 461
const splitY = (row: number) => row === 0 ? 280 : 730
</script>

<template>
  <svg class="agentic-factory" :class="{ 'is-static': complete }" viewBox="0 0 1760 990" role="img"
    aria-label="Intent flows through the queue, harness, automated checks, review, deployment, monitoring, and signals. Signals route incidents back into the queue. The final reveal circles review, where agentic code review and human review verify the change.">
    <defs>
      <marker :id="`${id}-arrow`" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="3" markerHeight="3" orient="auto">
        <path d="M 0 0 L 10 5 L 0 10 Z" fill="var(--ink-2)" />
      </marker>
      <template v-for="row in [0, 1]" :key="row">
        <clipPath :id="`${id}-art-${row}`">
          <rect :y="sourceY(row)" width="1672" :height="splitY(row) - sourceY(row)" />
        </clipPath>
        <clipPath :id="`${id}-text-${row}`">
          <rect :y="splitY(row)" width="1672" height="220" />
        </clipPath>
      </template>
    </defs>
    <g v-for="node in nodes" :key="node.name" v-click="node.step" :data-node="node.name"
      :aria-hidden="!shown(node.step)">
      <g v-for="layer in layers" :key="layer.name" class="cutout"
        :class="[layer.name, { visible: shown(node.step) }]"
        :style="{ animationDelay: shown(node.step) ? `${layer.delay}ms` : '0ms' }">
        <svg :x="40 + node.col * 440" :y="node.row === 0 ? 60 : 530" width="350"
          :height="350 * sourceHeight(node.row) / 418"
          :viewBox="`${node.col * 418} ${sourceY(node.row)} 418 ${sourceHeight(node.row)}`" overflow="hidden">
          <image :href="`${assetBase}images/agentic-software-factory/${layer.file}`" width="1672" height="941"
            :clip-path="layer.part ? `url(#${id}-${layer.part}-${node.row})` : undefined" />
        </svg>
      </g>
      <path v-if="node.arrow" class="connector" :class="{ visible: shown(node.step) }" :d="node.arrow"
        :marker-end="`url(#${id}-arrow)`" pathLength="1" />
    </g>

    <g v-click="8" class="feedback" :class="{ visible: shown(8) }" :aria-hidden="!shown(8)">
      <path d="M 380 560 C 450 490 530 500 650 425" :marker-end="`url(#${id}-arrow)`" />
      <text x="380" y="482" text-anchor="middle">route incidents in</text>
    </g>

    <g v-click="9" class="review-highlight" :class="{ visible: shown(9) }" :aria-hidden="!shown(9)"
      data-review-circle="true">
      <path d="M 1666 702 C 1574 663 1415 668 1350 711 C 1297 748 1311 824 1369 864 C 1435 907 1608 904 1693 859 C 1760 824 1762 755 1715 716 C 1699 702 1678 694 1654 690"
        pathLength="1" />
    </g>
  </svg>
</template>

<style scoped>
.agentic-factory { display: block; width: 100%; height: 100%; }
.cutout {
  --start: translateY(22px) rotate(-3deg) scale(.94);
  --over: translateY(-4px) rotate(1.5deg) scale(1.02);
  opacity: 0;
  transform-box: fill-box;
  transform-origin: center;
}
.cutout.visible { opacity: 1; animation: agentic-cutout-in 360ms steps(1, end) both; }
.teal { --start: translate(-16px, 12px) rotate(-4deg); }
.yellow { --start: scale(.7) rotate(7deg); --over: scale(1.05) rotate(-2deg); }
.art { --start: translateY(22px) rotate(3deg); }
.text { --start: translateY(12px) rotate(-3deg); }
@keyframes agentic-cutout-in {
  0% { opacity: 0; transform: var(--start); }
  1% { opacity: 1; transform: var(--start); }
  34% { opacity: 1; transform: var(--over); }
  67% { opacity: 1; transform: translateY(2px) rotate(-.5deg); }
  100% { opacity: 1; transform: none; }
}
.connector { fill: none; stroke: var(--ink-2); stroke-width: 7; stroke-linecap: round; stroke-dasharray: 1; stroke-dashoffset: 1; }
.connector.visible { stroke-dashoffset: 0; transition: stroke-dashoffset 320ms steps(4, end); }
.feedback { opacity: 0; }
.feedback.visible { opacity: 1; }
.feedback path { fill: none; stroke: var(--ink-3); stroke-width: 5; stroke-dasharray: 9 10; }
.feedback text { fill: var(--ink-2); font-family: var(--font-serif); font-size: 25px; font-style: italic; }
.review-highlight { opacity: 0; }
.review-highlight.visible { opacity: 1; }
.review-highlight path { fill: none; stroke: var(--signal-deep); stroke-width: 10; stroke-linecap: round; stroke-linejoin: round; stroke-dasharray: 1; stroke-dashoffset: 1; }
.review-highlight.visible path { stroke-dashoffset: 0; transition: stroke-dashoffset 650ms steps(8, end); }
.is-static .cutout.visible { animation: none; transform: none; }
.is-static .connector, .is-static .review-highlight path { transition: none; }
@media (prefers-reduced-motion: reduce) {
  .cutout { animation: none !important; transform: none; }
  .connector, .review-highlight path { transition: none !important; }
}
</style>
