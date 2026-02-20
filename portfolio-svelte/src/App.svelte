<script>
  import { writable } from 'svelte/store';
  import Hero from './lib/Hero.svelte';
  import PhotoViewer from './lib/PhotoViewer.svelte';
  import Footer from './lib/Footer.svelte';
  import { photos } from './lib/photoData.js';
  
  let currentIndex = 0;
  let hasInteracted = false;
  let showHero = true;
  
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
  
  function markInteracted() {
    hasInteracted = true;
  }
</script>

<div class="grain-overlay"></div>

{#if showHero}
  <Hero on:start={startExperience} />
{:else}
  <PhotoViewer 
    photo={photos[currentIndex]} 
    {currentIndex} 
    total={photos.length}
    {hasInteracted}
    on:next={nextPhoto}
    on:prev={prevPhoto}
    on:interact={markInteracted}
  />
  
  {#if currentIndex === photos.length - 1 && hasInteracted}
    <Footer />
  {/if}
{/if}


