<script>
  import { createEventDispatcher } from 'svelte';
  import { fade } from 'svelte/transition';
  
  const dispatch = createEventDispatcher();
  
  function startViewing() {
    dispatch('start');
  }
</script>

<section class="hero">
  <div class="hero-content" in:fade={{ duration: 1000 }}>
    <h1 class="hero-name">
      <span class="name-first">Zach</span>
      <span class="name-last">Lucas</span>
    </h1>
    <p class="hero-tagline">Fine Art Photography</p>
    
    <button class="start-button" on:click={startViewing} in:fade={{ duration: 800, delay: 600 }}>
      <span>Enter Gallery</span>
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
        <polyline points="9,18 15,12 9,6"></polyline>
      </svg>
    </button>
    
    <p class="hero-instruction" in:fade={{ duration: 800, delay: 800 }}>
      An immersive journey through 20 photographs
    </p>
  </div>
</section>

<style>
  .hero {
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    text-align: center;
    position: relative;
    overflow: hidden;
  }

  .hero::before {
    content: '';
    position: absolute;
    inset: 0;
    background: linear-gradient(135deg, rgba(255, 255, 255, 0.01) 0%, rgba(255, 255, 255, 0) 50%);
    pointer-events: none;
    animation: heroGradientShift 20s ease-in-out infinite alternate;
  }

  @keyframes heroGradientShift {
    0% { transform: translate(-10%, -10%) rotate(0deg); }
    100% { transform: translate(10%, 10%) rotate(5deg); }
  }

  .hero-content {
    max-width: 800px;
    margin: 0 auto;
    padding: 0 var(--spacing-unit);
    position: relative;
    z-index: 1;
  }

  .hero-name {
    font-family: var(--font-serif);
    font-size: clamp(3.5rem, 10vw, 7rem);
    font-weight: 300;
    letter-spacing: 0.1em;
    margin-bottom: 1rem;
    line-height: 1.1;
  }

  .hero-name .name-first {
    display: block;
  }

  .hero-name .name-last {
    display: block;
    margin-left: 2rem;
  }

  .hero-tagline {
    margin-top: 2rem;
    font-size: 0.9rem;
    letter-spacing: 0.3em;
    text-transform: uppercase;
    color: var(--text-muted);
    font-weight: 300;
  }

  .start-button {
    margin-top: 3rem;
    display: inline-flex;
    align-items: center;
    gap: 0.8rem;
    padding: 1rem 2rem;
    background: transparent;
    border: 2px solid var(--accent);
    color: var(--accent);
    font-family: var(--font-sans);
    font-size: 1rem;
    letter-spacing: 0.15em;
    text-transform: uppercase;
    cursor: pointer;
    transition: all 0.4s ease;
    position: relative;
    overflow: hidden;
  }
  
  .start-button::before {
    content: '';
    position: absolute;
    inset: 0;
    background: var(--accent);
    transform: translateX(-100%);
    transition: transform 0.4s ease;
    z-index: -1;
  }
  
  .start-button:hover {
    color: var(--bg);
    transform: translateY(-3px);
    box-shadow: 0 10px 30px rgba(161, 232, 135, 0.3);
  }
  
  .start-button:hover::before {
    transform: translateX(0);
  }
  
  .start-button svg {
    width: 1.5rem;
    height: 1.5rem;
    transition: transform 0.3s ease;
  }
  
  .start-button:hover svg {
    transform: translateX(5px);
  }
  
  .hero-instruction {
    margin-top: 2rem;
    font-size: 0.85rem;
    color: var(--text-muted);
    font-style: italic;
    letter-spacing: 0.05em;
  }
</style>
