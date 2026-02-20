<script>
  import { onMount } from 'svelte';
  import { fade, fly } from 'svelte/transition';
  
  export let photo;
  export let index;
  
  let imageElement;
  let aspectRatio = 'default';
  let isVisible = false;
  
  onMount(() => {
    const img = new Image();
    img.src = photo.src;
    img.onload = () => {
      const ratio = img.naturalWidth / img.naturalHeight;
      
      if (ratio > 2) {
        aspectRatio = 'ultra-wide';
      } else if (ratio > 1.5) {
        aspectRatio = 'wide';
      } else if (ratio > 1.2) {
        aspectRatio = 'large';
      } else if (ratio > 0.9 && ratio < 1.1) {
        aspectRatio = 'square';
      } else if (ratio > 0.7) {
        aspectRatio = 'tall';
      } else {
        aspectRatio = 'portrait';
      }
    };
    
    // Intersection Observer for scroll animations
    const observer = new IntersectionObserver(
      (entries) => {
        entries.forEach(entry => {
          if (entry.isIntersecting) {
            isVisible = true;
          }
        });
      },
      { threshold: 0.1, rootMargin: '0px 0px -50px 0px' }
    );
    
    if (imageElement) {
      observer.observe(imageElement);
    }
    
    return () => observer.disconnect();
  });
</script>

<article 
  class="photo-section" 
  data-section={String(index + 1).padStart(2, '0')}
  bind:this={imageElement}
>
  {#if isVisible}
    <div class="photo-container" in:fly={{ y: 30, duration: 800, delay: index * 50 }}>
      <div class="photo-wrapper {aspectRatio}">
        <img 
          src={photo.src} 
          alt={`${photo.title} - Photography by Zach Lucas`}
          class="photo-img"
          loading="lazy"
        />
        
        <div class="photo-overlay"></div>
        
        <div class="photo-info">
          <h3 class="photo-title">{photo.title}</h3>
          <p class="photo-description">{photo.description}</p>
        </div>
      </div>
      
      <div class="photo-about" in:fade={{ duration: 600, delay: 400 }}>
        <h3 class="about-title">{photo.title}</h3>
        <p class="about-thoughts">{photo.thoughts}</p>
        
        <div class="about-details">
          <div class="about-detail-item">
            <span class="detail-label">Location:</span>
            <span class="detail-value">{photo.location}</span>
          </div>
          <div class="about-detail-item">
            <span class="detail-label">Camera:</span>
            <span class="detail-value">{photo.camera}</span>
          </div>
          <div class="about-detail-item">
            <span class="detail-label">Index:</span>
            <span class="detail-value">{index + 1} of {20}</span>
          </div>
        </div>
      </div>
    </div>
  {/if}
</article>

<style>
  /* Component styles are scoped to this component */
</style>
