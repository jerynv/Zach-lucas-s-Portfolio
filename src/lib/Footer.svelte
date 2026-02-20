<script>
  import { fade } from 'svelte/transition';
</script>

<footer class="footer" in:fade={{ duration: 1000, delay: 1000 }}>
  <div class="footer-inner">
    <div class="footer-rule">
      <div class="rule-line"></div>
      <div class="rule-diamond"></div>
      <div class="rule-line"></div>
    </div>
    <h3 class="footer-name">Zach Lucas</h3>
    <p class="footer-tagline">Fine Art Photography</p>
  </div>
</footer>

<style>
  .footer {
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
    z-index: 200;
    background: var(--bg);
    padding: 2rem;
    text-align: center;
  }

  .footer-inner {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0.75rem;
  }

  .footer-rule {
    display: flex;
    align-items: center;
    gap: 0.5rem;
  }

  .rule-line {
    width: 1.5rem;
    height: 1px;
    background: rgba(232, 224, 208, 0.15);
  }

  .rule-diamond {
    width: 4px;
    height: 4px;
    border: 1px solid var(--amber);
    transform: rotate(45deg);
    opacity: 0.6;
  }

  .footer-name {
    font-family: var(--font-serif);
    font-size: 1.1rem;
    font-weight: 400;
    letter-spacing: 0.1em;
    color: var(--paper);
  }

  .footer-tagline {
    font-family: var(--font-sans);
    font-size: 0.7rem;
    letter-spacing: 0.25em;
    text-transform: uppercase;
    color: var(--text-muted);
  }
</style>
