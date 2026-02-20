<script>
  import Hero from './lib/Hero.svelte';
  import PhotoViewer from './lib/PhotoViewer.svelte';
  import About from './lib/About.svelte';
  import Footer from './lib/Footer.svelte';
  import { photos } from './lib/photoData.js';

  let currentIndex = 0;
  let hasInteracted = false;
  let showHero = true;
  let showAbout = false;

  function startExperience() {
    showHero = false;
  }

  function nextPhoto() {
    if (currentIndex < photos.length - 1) {
      currentIndex++;
      hasInteracted = false;
    }
  }

  function prevPhoto() {
    if (currentIndex > 0) {
      currentIndex--;
      hasInteracted = false;
    }
  }

  function jumpToPhoto(e) {
    const idx = e.detail;
    if (idx !== currentIndex) {
      currentIndex = idx;
      hasInteracted = false;
    }
  }

  function markInteracted() {
    hasInteracted = true;
  }

  function closeViewer() {
    showHero = true;
    currentIndex = 0;
    hasInteracted = false;
  }
</script>

<div class="grain-overlay"></div>

{#if showHero}
  <Hero on:start={startExperience} on:about={() => showAbout = true} />
{:else}
  <PhotoViewer
    photo={photos[currentIndex]}
    {photos}
    {currentIndex}
    total={photos.length}
    {hasInteracted}
    on:next={nextPhoto}
    on:prev={prevPhoto}
    on:interact={markInteracted}
    on:jump={jumpToPhoto}
    on:close={closeViewer}
    on:about={() => showAbout = true}
  />

  {#if currentIndex === photos.length - 1 && hasInteracted}
    <Footer />
  {/if}
{/if}

{#if showAbout}
  <About on:close={() => showAbout = false} />
{/if}
