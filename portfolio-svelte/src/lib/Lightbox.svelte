<script>
  import { onMount } from 'svelte';
  import { fly, fade, scale } from 'svelte/transition';
  
  export let photos = [];
  export let currentIndex = 0;
  export let isOpen = false;
  
  function close() {
    isOpen = false;
  }
  
  function next() {
    currentIndex = (currentIndex + 1) % photos.length;
  }
  
  function prev() {
    currentIndex = (currentIndex - 1 + photos.length) % photos.length;
  }
  
  function handleKeydown(e) {
    if (!isOpen) return;
    if (e.key === 'Escape') close();
    if (e.key === 'ArrowRight') next();
    if (e.key === 'ArrowLeft') prev();
  }
  
  onMount(() => {
    window.addEventListener('keydown', handleKeydown);
    return () => window.removeEventListener('keydown', handleKeydown);
  });
  
  $: if (isOpen) {
    document.body.style.overflow = 'hidden';
  } else {
    document.body.style.overflow = '';
  }
</script>

{#if isOpen}
  <!-- svelte-ignore a11y-click-events-have-key-events -->
  <!-- svelte-ignore a11y-no-static-element-interactions -->
  <div class="lightbox" on:click={close} transition:fade={{ duration: 300 }}>
    <button class="lightbox-close" on:click={close} aria-label="Close lightbox">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
        <line x1="18" y1="6" x2="6" y2="18"></line>
        <line x1="6" y1="6" x2="18" y2="18"></line>
      </svg>
    </button>
    
    <button class="lightbox-nav lightbox-prev" on:click|stopPropagation={prev} aria-label="Previous image">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
        <polyline points="15,18 9,12 15,6"></polyline>
      </svg>
    </button>
    
    <!-- svelte-ignore a11y-click-events-have-key-events -->
    <!-- svelte-ignore a11y-no-static-element-interactions -->
    <div class="lightbox-content" on:click|stopPropagation>
      {#key currentIndex}
        <img 
          src={photos[currentIndex]?.src} 
          alt={photos[currentIndex]?.title}
          class="lightbox-img"
          in:scale={{ duration: 400, start: 0.95 }}
        />
      {/key}
    </div>
    
    <button class="lightbox-nav lightbox-next" on:click|stopPropagation={next} aria-label="Next image">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
        <polyline points="9,18 15,12 9,6"></polyline>
      </svg>
    </button>
    
    <div class="lightbox-counter">{currentIndex + 1} of {photos.length}</div>
  </div>
{/if}
