<script setup lang="ts">
import { computed, useId } from 'vue'
import { useSlideContext } from '@slidev/client'

const { $clicks, $renderContext } = useSlideContext()
const instanceId = `sdlc-${useId().replace(/[^a-zA-Z0-9-]/g, '')}`
const complete = computed(() => ['overview', 'previewNext', 'print'].includes($renderContext.value))
const shown = (step: number) => complete.value || $clicks.value >= step
const timing = (step: number, delay: number) => ({ animationDelay: shown(step) ? `${delay}ms` : '0ms' })

// Coordinates refer to the three registered 1672 × 941 transparent image plates.
// Each stage gets its own viewport, so every piece can move independently.
const stages = [
  { name: 'Plan', x: 580, y: 0, w: 520, h: 340, labelX: 835, labelY: 327, split: 850 },
  { name: 'Design', x: 1170, y: 110, w: 490, h: 355, labelX: 1415, labelY: 450, split: 1440 },
  { name: 'Build', x: 1160, y: 480, w: 500, h: 370, labelX: 1420, labelY: 863, split: 1450 },
  { name: 'Test', x: 600, y: 570, w: 510, h: 360, labelX: 850, labelY: 916, split: 860 },
  { name: 'Deploy', x: 30, y: 480, w: 480, h: 370, labelX: 260, labelY: 856, split: 330 },
  { name: 'Monitor', x: 20, y: 115, w: 500, h: 350, labelX: 270, labelY: 450, split: 340 },
]
const arrows = [
  'M 1062 103 C 1131 108 1192 135 1230 171',
  'M 1572 449 C 1589 477 1592 498 1583 520',
  'M 1215 807 C 1170 836 1133 844 1091 840',
  'M 603 839 C 546 837 499 815 463 791',
  'M 124 527 C 110 493 110 463 120 432',
  'M 435 182 C 486 145 534 126 583 122',
]
const layers = [
  { name: 'teal', delay: 0 },
  { name: 'yellow', delay: 140 },
  { name: 'primary', delay: 280 },
  { name: 'detail', delay: 440 },
] as const
const stageStep = (index: number) => 3 + index * 2
const clipId = (index: number, part: string) => `${instanceId}-${index}-${part}`
</script>

<template>
  <svg class="sdlc-loop" :class="{ 'is-static': complete }" viewBox="0 0 1672 941" role="img"
    aria-label="Software development life cycle: Plan, Design, Build, Test, Deploy, Monitor, then back to Plan.">
    <defs>
      <marker :id="`${instanceId}-arrow`" viewBox="0 0 10 10" refX="8" refY="5"
        markerWidth="3.2" markerHeight="3.2" orient="auto-start-reverse">
        <path d="M 0 0 L 10 5 L 0 10 z" fill="var(--signal-deep)" />
      </marker>
      <template v-for="(stage, index) in stages" :key="stage.name">
        <clipPath :id="clipId(index, 'primary')" clipPathUnits="userSpaceOnUse">
          <rect :x="stage.x" :y="stage.y" :width="stage.split - stage.x" :height="stage.h" />
        </clipPath>
        <clipPath :id="clipId(index, 'detail')" clipPathUnits="userSpaceOnUse">
          <rect :x="stage.split" :y="stage.y" :width="stage.x + stage.w - stage.split" :height="stage.h" />
        </clipPath>
      </template>
    </defs>

    <g v-click="1" class="piece center-label" :class="{ visible: shown(1) }" :aria-hidden="!shown(1)">
      <path d="M 600 390 L 736 378 L 862 386 L 1069 380 L 1057 555 L 929 550 L 794 563 L 601 553 Z"
        fill="var(--card)" />
      <path d="M 678 547 L 995 544" stroke="var(--highlighter)" stroke-width="13" />
      <text x="836" y="522" text-anchor="middle" class="center-text">SDLC</text>
    </g>
    <g v-click="2" class="piece shine" :class="{ visible: shown(2) }" :aria-hidden="!shown(2)"
      stroke="var(--ink)" stroke-width="7" stroke-linecap="round">
      <path d="M 571 438 L 551 417 M 565 472 L 528 466 M 568 504 L 548 516" />
      <path d="M 1095 438 L 1115 417 M 1102 472 L 1139 466 M 1099 504 L 1120 516" />
    </g>

    <g v-for="(stage, index) in stages" :key="stage.name" v-click="stageStep(index)" :data-stage="stage.name"
      :aria-hidden="!shown(stageStep(index))">
      <g v-for="layer in layers" :key="layer.name" class="piece stage-piece"
        :class="[layer.name, { visible: shown(stageStep(index)) }]"
        :style="timing(stageStep(index), layer.delay)">
        <svg :x="stage.x" :y="stage.y" :width="stage.w" :height="stage.h"
          :viewBox="`${stage.x} ${stage.y} ${stage.w} ${stage.h}`" overflow="hidden">
          <image width="1672" height="941"
            :href="`/images/sdlc/layers/${layer.name === 'primary' || layer.name === 'detail' ? 'objects' : layer.name}.png`"
            :clip-path="layer.name === 'primary' || layer.name === 'detail' ? `url(#${clipId(index, layer.name)})` : undefined" />
        </svg>
      </g>
      <g class="piece stage-name" :class="{ visible: shown(stageStep(index)) }"
        :style="timing(stageStep(index), 600)">
        <path :d="`M ${stage.labelX - 133} ${stage.labelY - 43} l 82 -4 l 83 3 l 104 -3 l -3 62 l -121 3 l -142 -3 Z`"
          fill="var(--card)" />
        <text :x="stage.labelX" :y="stage.labelY" text-anchor="middle">{{ stage.name }}</text>
      </g>
      <g v-click="stageStep(index) + 1" class="piece arrow" :class="{ visible: shown(stageStep(index) + 1) }"
        :aria-hidden="!shown(stageStep(index) + 1)">
        <path :d="arrows[index]" fill="none" stroke="var(--signal-deep)" stroke-width="14"
          stroke-linecap="round" :marker-end="`url(#${instanceId}-arrow)`" pathLength="1" />
      </g>
    </g>
  </svg>
</template>

<style scoped>
.sdlc-loop { display: block; width: 100%; height: 100%; overflow: visible; }
.piece {
  --cutout-start: translateY(18px) rotate(-3deg) scale(.94);
  --cutout-over: translateY(-4px) rotate(1.5deg) scale(1.02);
  --cutout-settle: translateY(2px) rotate(-.7deg);
  opacity: 0;
  transform-box: fill-box;
  transform-origin: center;
}
.piece.visible {
  opacity: 1;
  animation: cutout-in 360ms steps(1, end) both;
}
/* Held poses create a cut-paper stop-motion entrance. Nothing drifts at rest. */
@keyframes cutout-in {
  0% { opacity: 0; transform: var(--cutout-start); }
  1% { opacity: 1; transform: var(--cutout-start); }
  34% { opacity: 1; transform: var(--cutout-over); }
  67% { opacity: 1; transform: var(--cutout-settle); }
  100% { opacity: 1; transform: none; }
}
.center-label { --cutout-start: translateY(22px) scale(.86) rotate(-4deg); }
.center-text { font-family: var(--font-display); font-size: 143px; font-weight: 900; letter-spacing: -7px; fill: var(--ink); }
.shine { --cutout-start: scale(.65) rotate(-5deg); --cutout-over: scale(1.1) rotate(2deg); }
.teal { --cutout-start: translate(-18px, 14px) rotate(-5deg); --cutout-over: translate(4px, -3px) rotate(2deg); }
.yellow { --cutout-start: scale(.65) rotate(9deg); --cutout-over: scale(1.06) rotate(-3deg); }
.primary { --cutout-start: translateY(25px) rotate(4deg); }
.detail { --cutout-start: translate(20px, -12px) rotate(-7deg); --cutout-over: translate(-3px, 3px) rotate(2deg); }
.stage-name { --cutout-start: translateY(12px) rotate(-4deg); }
.stage-name text { font-family: var(--font-display); font-size: 44px; font-weight: 850; letter-spacing: -1.5px; fill: var(--ink); }
.arrow.visible { animation: none; }
.arrow path { stroke-dasharray: 1; stroke-dashoffset: 1; }
.arrow.visible path { stroke-dashoffset: 0; transition: stroke-dashoffset 320ms steps(4, end); }
.is-static .piece.visible { animation: none; transform: none; }
.is-static .arrow path { transition: none; }
@media (prefers-reduced-motion: reduce) {
  .piece, .arrow path { animation: none !important; transition: none !important; transform: none; }
}
</style>
