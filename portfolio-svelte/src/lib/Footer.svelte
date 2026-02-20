<script>
  import { fade } from 'svelte/transition';
</script>

<footer class="footer" in:fade={{ duration: 1000, delay: 1000 }}>
  <div class="container">
    <div class="footer-content">
      <h3 class="footer-name">Zach Lucas</h3>
      <p class="footer-tagline">Fine Art Photography</p>
    </div>
  </div>
</footer>
