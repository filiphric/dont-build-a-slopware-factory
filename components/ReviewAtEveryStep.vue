<script setup lang="ts">
const steps = [
  { number: '01', title: 'Ticket', subtitle: "What's asked", question: 'Is the ask clear and complete?', click: 4 },
  { number: '02', title: 'Plan', subtitle: "How it'll be built", question: 'Does the plan fit the ticket?', click: 3 },
  { number: '03', title: 'Uncommitted changes', subtitle: 'Work in progress', question: 'Is the change right so far?', click: 2 },
  { number: '04', title: 'Pull request', subtitle: 'Ready for merge', question: 'Is it ready to merge?', click: 1 },
] as const
</script>

<template>
  <div class="review-at-every-step" aria-label="Review at every step">
    <div class="review-grid">
      <img class="band verification-band" src="/images/paper-panels/verification-paper.png" alt="" aria-hidden="true" />
      <img class="band generation-band" src="/images/paper-panels/generation-paper-green.png" alt="" aria-hidden="true" />

      <div class="row-label verification-label">
        <span>Verification</span>
        <small>checks</small>
      </div>
      <div class="row-label generation-label">
        <span>Generation</span>
        <small>produces</small>
      </div>

      <section
        v-for="(step, index) in steps"
        :key="step.number"
        v-click="step.click"
        class="step"
        :style="{ gridColumn: index + 2 }"
        :aria-label="step.title"
      >
        <div class="review-card">
          <span class="review-number">REVIEW {{ step.number }}</span>
          <span class="review-question">{{ step.question }}</span>
        </div>
        <div class="review-arrow" aria-hidden="true">
          <svg viewBox="0 0 24 72"><path d="M12 0V55" /><path d="M2 54L12 70L22 54Z" /></svg>
        </div>
        <div class="generation-card">
          <span class="step-number">{{ step.number }}</span>
          <h2>{{ step.title }}</h2>
          <span class="step-subtitle">{{ step.subtitle }}</span>
          <svg v-if="index < steps.length - 1" class="next-arrow" viewBox="0 0 28 24" aria-hidden="true">
            <path d="M0 12H14" /><path d="M13 4L26 12L13 20Z" />
          </svg>
        </div>
      </section>
    </div>
  </div>
</template>

<style scoped>
.review-at-every-step {
  position: absolute;
  inset: 0;
  display: flex;
  align-items: center;
  padding: 48px 40px 72px;
}

.review-grid {
  width: 100%;
  display: grid;
  grid-template-columns: 152px repeat(4, minmax(0, 1fr));
  grid-template-rows: 154px 88px 170px;
  column-gap: 28px;
  padding: 24px;
  position: relative;
  isolation: isolate;
}

.band {
  position: absolute;
  left: 0;
  right: 0;
  width: 100%;
  height: 202px;
  max-width: none;
  object-fit: fill;
  pointer-events: none;
  z-index: 0;
}

.verification-band { top: 0; }
.generation-band { bottom: 0; height: 218px; }

.row-label {
  position: relative;
  z-index: 1;
  grid-column: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  gap: 6px;
}

.verification-label { grid-row: 1; }
.generation-label { grid-row: 3; }
.row-label > span { font: 16px 'Silkscreen', var(--font-mono); text-transform: uppercase; }
.row-label small { font: 14px var(--font-mono); }

.step {
  position: relative;
  z-index: 1;
  min-width: 0;
  grid-row: 1 / 4;
  display: grid;
  grid-template-rows: subgrid;
  transition: none;
}

.step:not(.slidev-vclick-hidden) {
  animation: paper-reveal 360ms steps(1, end) both;
}

@keyframes paper-reveal {
  0% { opacity: 0; transform: translateY(22px) rotate(-2deg) scale(.96); }
  1% { opacity: 1; transform: translateY(22px) rotate(-2deg) scale(.96); }
  34% { opacity: 1; transform: translateY(-4px) rotate(1deg) scale(1.015); }
  67% { opacity: 1; transform: translateY(2px) rotate(-.5deg); }
  100% { opacity: 1; transform: none; }
}

.step.slidev-vclick-hidden {
  opacity: 0;
  visibility: hidden;

}

.review-card, .generation-card {
  min-width: 0;
  display: flex;
  flex-direction: column;
  border: 2px solid var(--ink);
  box-shadow: 4px 4px 0 var(--ink);
  padding: 14px;
}

.review-card { background: var(--ink); gap: 8px; font-family: 'VT323', var(--font-mono); }
.review-number { font-size: 20px; color: var(--highlighter); }
.review-question { font-size: 22px; line-height: 1.1; color: var(--card); }
.review-arrow { padding: 8px 0; color: var(--signal); }
.review-arrow svg { display: block; height: 100%; width: 24px; margin: 0 auto; }
svg { fill: currentColor; stroke: currentColor; stroke-width: 3; }

.generation-card {
  position: relative;
  justify-content: center;
  gap: 10px;
  background: var(--card);
}

.step-number { font: 14px 'Silkscreen', var(--font-mono); color: var(--signal); }

.generation-card h2 {
  margin: 0;
  font-family: var(--font-display);
  font-size: 22px;
  font-weight: 900;
  line-height: 1;
  letter-spacing: -0.02em;
  overflow-wrap: anywhere;
}

.step-subtitle { font: 13px var(--font-mono); color: var(--ink-2); }
.next-arrow { position: absolute; left: calc(100% + 4px); top: calc(50% - 12px); width: 24px; height: 24px; color: var(--ink); }

@media (prefers-reduced-motion: reduce) {
  .step { animation: none !important; transform: none; }
}
</style>
