<script setup lang="ts">
const inputs = [
  { name: 'Skills', detail: 'How we build' },
  { name: 'Rules', detail: "What's allowed" },
  { name: 'Ticket', detail: "What's asked" },
  { name: 'Docs', detail: 'How it works' },
] as const

const checks = [
  { action: 'LOAD SKILLS', result: 'APPLIED' },
  { action: 'LOAD RULES', result: 'FOLLOWED' },
  { action: 'READ TICKET', result: 'SATISFIED' },
  { action: 'READ DOCS', result: 'CONSISTENT' },
] as const
</script>

<template>
  <div class="generation-verification" aria-label="Generation and verification">
    <section v-click="1" class="zone generation">
      <header class="zone-label">
        <span>Generation</span>
        <small>writes the code</small>
      </header>
      <div v-click="3" class="agent-window">
        <div class="window-bar" aria-hidden="true">
          <i class="close-box" /><i class="stripes" />
          <span>Coding agent</span><i class="stripes" />
        </div>
        <div class="window-body">
          <h2>Coding agent</h2>
          <div class="context-label">Context in</div>
          <div class="inputs">
            <div v-for="input in inputs" :key="input.name" class="input-card">
              <strong>▸ {{ input.name }}</strong>
              <span>{{ input.detail }}</span>
            </div>
          </div>
        </div>
      </div>
    </section>

    <div v-click="2" class="handoff">
      <div class="connection">
        <span>Code</span>
        <svg viewBox="0 0 120 24" aria-hidden="true">
          <path d="M0 12H104" /><path d="M103 2L119 12L103 22Z" />
        </svg>
      </div>
      <div class="connection feedback">
        <svg viewBox="0 0 120 24" aria-hidden="true">
          <path d="M16 12H120" /><path d="M17 2L1 12L17 22Z" />
        </svg>
        <span>Feedback</span>
      </div>
    </div>

    <section v-click="2" class="zone verification">
      <header class="zone-label">
        <span>Verification</span>
        <small>checks the code</small>
      </header>
      <div v-click="4" class="agent-window">
        <div class="window-bar" aria-hidden="true">
          <i class="close-box" /><i class="stripes" />
          <span>Code review agent</span><i class="stripes" />
        </div>
        <div class="window-body terminal">
          <h2>Code review agent</h2>
          <div v-for="check in checks" :key="check.action" class="check">
            <span>&gt; {{ check.action }}</span>
            <span>✓ {{ check.result }}</span>
          </div>
          <div class="terminal-prompt">▸ CHECK EVERYTHING <span aria-hidden="true">█</span></div>
        </div>
      </div>
    </section>
  </div>
</template>

<style scoped>
.generation-verification {
  position: absolute;
  inset: 0;
  padding: 48px 56px 72px;
  display: grid;
  grid-template-columns: minmax(0, 1fr) 140px minmax(0, 1fr);
  align-content: center;
  font-family: var(--font-display);
}

.zone {
  position: relative;
  isolation: isolate;
  min-width: 0;
  padding: 24px;
}

.zone::before {
  content: '';
  position: absolute;
  inset: -8px;
  z-index: -1;
  background: var(--zone-paper) center / 100% 100% no-repeat;
  pointer-events: none;
}

.generation { --zone-paper: url('/images/paper-panels/generation-paper-green.png'); }
.verification { --zone-paper: url('/images/paper-panels/verification-paper.png'); }

.zone-label {
  display: flex;
  align-items: baseline;
  justify-content: space-between;
  gap: 8px;
  margin-bottom: 18px;
}

.zone-label > span {
  font: 20px 'Silkscreen', var(--font-mono);
  text-transform: uppercase;
  letter-spacing: 0.02em;
}

.zone-label small {
  font: 12px var(--font-mono);
  white-space: nowrap;
}

.agent-window {
  border: 2px solid var(--ink);
  background: var(--card);
  box-shadow: 5px 5px 0 var(--ink);
}

.window-bar {
  display: flex;
  align-items: center;
  gap: 6px;
  height: 24px;
  padding: 0 7px;
  border-bottom: 2px solid var(--ink);
  font: 10px 'Silkscreen', var(--font-mono);
  text-transform: uppercase;
}

.close-box {
  width: 12px;
  height: 12px;
  border: 2px solid var(--ink);
}

.stripes {
  flex: 1;
  height: 10px;
  background: repeating-linear-gradient(to bottom, var(--ink) 0 1px, transparent 1px 3px);
}

.window-body {
  padding: 20px;
  min-height: 278px;
}

.window-body h2 {
  margin: 0 0 18px;
  font-size: 32px;
  font-weight: 900;
  line-height: 1.05;
  letter-spacing: -0.02em;
}

.context-label {
  margin-bottom: 12px;
  font: 600 12px var(--font-mono);
  letter-spacing: 0.08em;
  text-transform: uppercase;
  color: var(--ink-3);
}

.inputs {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
}

.input-card {
  display: flex;
  flex-direction: column;
  gap: 4px;
  padding: 12px;
  border: 2px solid var(--ink);
  background: var(--paper-2);
}

.input-card strong {
  font: 700 16px var(--font-mono);
  text-transform: uppercase;
}

.input-card > span { font-size: 16px; color: var(--ink-2); }

.terminal {
  background: var(--ink);
  color: var(--card);
  font: 25px / 1.2 'VT323', var(--font-mono);
}

.terminal h2 { color: var(--paper); }
.check { display: flex; justify-content: space-between; gap: 8px; margin-bottom: 7px; }

.terminal-prompt {
  margin-top: 16px;
  padding-top: 14px;
  border-top: 1px solid var(--ink-3);
  color: var(--highlighter);
}

.handoff {
  display: flex;
  flex-direction: column;
  justify-content: center;
  gap: 34px;
  padding: 48px 14px 0;
}

.connection { display: flex; flex-direction: column; gap: 7px; }
.connection > span { font: 700 13px var(--font-mono); text-align: center; text-transform: uppercase; }
.connection svg { width: 100%; fill: currentColor; stroke: currentColor; stroke-width: 3; }
.feedback { color: var(--signal); }

.zone, .handoff, .agent-window {
  transition: none;
}

.zone:not(.slidev-vclick-hidden), .handoff:not(.slidev-vclick-hidden), .agent-window:not(.slidev-vclick-hidden) {
  animation: paper-reveal 360ms steps(1, end) both;
}

@keyframes paper-reveal {
  0% { opacity: 0; transform: translateY(22px) rotate(-2deg) scale(.96); }
  1% { opacity: 1; transform: translateY(22px) rotate(-2deg) scale(.96); }
  34% { opacity: 1; transform: translateY(-4px) rotate(1deg) scale(1.015); }
  67% { opacity: 1; transform: translateY(2px) rotate(-.5deg); }
  100% { opacity: 1; transform: none; }
}

.zone.slidev-vclick-hidden, .handoff.slidev-vclick-hidden, .agent-window.slidev-vclick-hidden {
  opacity: 0;
  visibility: hidden;

}

@media (prefers-reduced-motion: reduce) {
  .zone, .handoff, .agent-window { animation: none !important; transform: none; }
}
</style>
