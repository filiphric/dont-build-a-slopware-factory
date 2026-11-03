<template>
  <div class="slidev-layout editorial section">
    <main class="editorial-content"><slot /></main>
    <footer class="editorial-footer">
      <span class="editorial-wordmark">filiphric.com</span>
      <span>{{ $slidev.configs.event || $slidev.configs.title }}</span>
    </footer>
  </div>
</template>
