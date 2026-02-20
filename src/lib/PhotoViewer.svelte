<script>
  import { onMount, createEventDispatcher, tick } from 'svelte';
  import { fade, fly } from 'svelte/transition';

  export let photo;
  export let photos = [];
  export let currentIndex;
  export let total;
  export let hasInteracted = false;

  const dispatch = createEventDispatcher();

  let imageLoaded = false;
  let aspectRatio = 'default';
  let stripContainer;
  let showDetails = false;
  let interactTimer;

  function goNext() {
    if (currentIndex < total - 1) {
      dispatch('next');
      showDetails = false;
    }
  }

  function goPrev() {
    if (currentIndex > 0) {
      dispatch('prev');
      showDetails = false;
    }
  }

  function jumpTo(index) {
    if (index !== currentIndex) {
      dispatch('jump', index);
      showDetails = false;
    }
  }

  function toggleDetails() {
    showDetails = !showDetails;
    if (!hasInteracted) dispatch('interact');
  }

  function handleKeydown(e) {
    if (e.key === 'ArrowRight') goNext();
    else if (e.key === 'ArrowLeft') goPrev();
    else if (e.key === 'Escape' && showDetails) showDetails = false;
    else if (e.key === ' ') { e.preventDefault(); toggleDetails(); }
  }

  function detectAspectRatio(src) {
    imageLoaded = false;
    aspectRatio = 'default';
    const img = new Image();
    img.src = src;
    img.onload = () => {
      const ratio = img.naturalWidth / img.naturalHeight;
      if (ratio > 2) aspectRatio = 'ultra-wide';
      else if (ratio > 1.5) aspectRatio = 'wide';
      else if (ratio > 1.2) aspectRatio = 'large';
      else if (ratio > 0.9 && ratio < 1.1) aspectRatio = 'square';
      else if (ratio > 0.7) aspectRatio = 'tall';
      else aspectRatio = 'portrait';
      imageLoaded = true;
    };
  }

  $: detectAspectRatio(photo.src);

  // Auto-scroll film strip to center current frame
  $: scrollToFrame(currentIndex);

  function scrollToFrame(idx) {
    if (!stripContainer) return;
    tick().then(() => {
      const frame = stripContainer.children[idx];
      if (frame) {
        frame.scrollIntoView({
          behavior: 'smooth',
          block: 'nearest',
          inline: 'center'
        });
      }
    });
  }

  // Auto-interact after a moment of viewing
  $: scheduleInteract(hasInteracted);

  function scheduleInteract(interacted) {
    clearTimeout(interactTimer);
    if (!interacted) {
      interactTimer = setTimeout(() => dispatch('interact'), 3000);
    }
  }

  onMount(() => {
    window.addEventListener('keydown', handleKeydown);
    scrollToFrame(currentIndex);
    return () => {
      window.removeEventListener('keydown', handleKeydown);
      clearTimeout(interactTimer);
    };
  });
</script>

<div class="photo-viewer" in:fade={{ duration: 600 }}>
  <!-- Progress bar -->
  <div class="progress-bar">
    <div class="progress-fill" style="width: {((currentIndex + 1) / total) * 100}%"></div>
  </div>

  <!-- Close button -->
  <button class="close-btn" on:click={() => dispatch('close')} aria-label="Back to home">
    <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
      <line x1="6" y1="6" x2="18" y2="18"/>
      <line x1="6" y1="18" x2="18" y2="6"/>
    </svg>
  </button>

  <!-- Top right -->
  <div class="top-right" in:fade={{ delay: 200 }}>
    <button class="about-link" on:click={() => dispatch('about')}>About</button>
    <span class="counter">{String(currentIndex + 1).padStart(2, '0')} / {String(total).padStart(2, '0')}</span>
  </div>

  <!-- Main photo area -->
  <div class="viewer-main">
    <!-- Navigation -->
    {#if currentIndex > 0}
      <button class="nav-btn nav-prev" on:click={goPrev} aria-label="Previous photo" transition:fade={{ duration: 200 }}>
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
          <polyline points="15,18 9,12 15,6"/>
        </svg>
      </button>
    {/if}
    {#if currentIndex < total - 1}
      <button class="nav-btn nav-next" on:click={goNext} aria-label="Next photo" transition:fade={{ duration: 200 }}>
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
          <polyline points="9,18 15,12 9,6"/>
        </svg>
      </button>
    {/if}

    <!-- Photo -->
    {#key photo.src}
      <!-- svelte-ignore a11y_click_events_have_key_events -->
      <!-- svelte-ignore a11y_no_static_element_interactions -->
      <div class="photo-frame {aspectRatio}" on:click={toggleDetails} in:fade={{ duration: 500 }}>
        <img src={photo.src} alt={photo.title} class="main-image" />
      </div>
    {/key}
  </div>

  <!-- Info bar -->
  <!-- svelte-ignore a11y_click_events_have_key_events -->
  <!-- svelte-ignore a11y_no_static_element_interactions -->
  <div class="info-bar" on:click={toggleDetails}>
    <div class="info-row">
      <div class="info-left">
        <span class="info-number">{String(currentIndex + 1).padStart(2, '0')}</span>
        <div class="info-text">
          <h2 class="info-title">{photo.title}</h2>
          <p class="info-desc">{photo.description}</p>
        </div>
      </div>
      <div class="info-right">
        <span class="info-meta">{photo.camera}</span>
        <span class="info-sep">&middot;</span>
        <span class="info-meta">{photo.location}</span>
        <button
          class="details-btn"
          class:open={showDetails}
          on:click|stopPropagation={toggleDetails}
          aria-label="Toggle details"
        >
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
            <polyline points="6,9 12,15 18,9"/>
          </svg>
        </button>
      </div>
    </div>

    {#if showDetails}
      <div class="info-expanded" transition:fly={{ y: 8, duration: 250 }}>
        <blockquote class="info-thoughts">"{photo.thoughts}"</blockquote>
      </div>
    {/if}
  </div>

  <!-- Film strip -->
  <div class="film-strip">
    <div class="strip-scroll" bind:this={stripContainer}>
      {#each photos as p, i}
        <button
          class="strip-frame"
          class:active={i === currentIndex}
          on:click={() => jumpTo(i)}
          aria-label="Photo {i + 1}: {p.title}"
        >
          <img src={p.src} alt="" loading="lazy" />
          <span class="frame-num">{String(i + 1).padStart(2, '0')}</span>
        </button>
      {/each}
    </div>
  </div>
</div>

<style>
  .photo-viewer {
    position: fixed;
    inset: 0;
    background: var(--bg);
    display: flex;
    flex-direction: column;
    z-index: 100;
  }

  /* ── Progress ── */
  .progress-bar {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    height: 2px;
    background: rgba(232, 224, 208, 0.06);
    z-index: 1000;
  }

  .progress-fill {
    height: 100%;
    background: var(--amber);
    transition: width 0.5s var(--ease);
  }

  /* ── Close ── */
  .close-btn {
    position: absolute;
    top: 1.25rem;
    left: 1.5rem;
    width: 2.5rem;
    height: 2.5rem;
    background: none;
    border: 1px solid rgba(232, 224, 208, 0.1);
    border-radius: 50%;
    color: var(--text-muted);
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: all 0.3s ease;
    z-index: 1000;
  }

  .close-btn:hover {
    border-color: var(--amber);
    color: var(--amber);
  }

  .close-btn svg {
    width: 0.9rem;
    height: 0.9rem;
  }

  /* ── Top Right ── */
  .top-right {
    position: absolute;
    top: 1.75rem;
    right: 1.5rem;
    display: flex;
    align-items: center;
    gap: 1.25rem;
    z-index: 1000;
  }

  .counter {
    font-family: var(--font-mono);
    font-size: 0.75rem;
    letter-spacing: 0.2em;
    color: var(--text-muted);
  }

  .about-link {
    font-family: var(--font-mono);
    font-size: 0.65rem;
    letter-spacing: 0.2em;
    text-transform: uppercase;
    color: var(--text-muted);
    background: none;
    border: none;
    cursor: pointer;
    padding: 0;
    transition: color 0.3s ease;
  }

  .about-link:hover {
    color: var(--amber);
  }

  /* ── Main Photo Area ── */
  .viewer-main {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    position: relative;
    overflow: hidden;
    min-height: 0;
    padding: 1rem 0;
  }

  /* ── Navigation ── */
  .nav-btn {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    width: 2.5rem;
    height: 4rem;
    background: none;
    border: none;
    color: var(--paper);
    opacity: 0.2;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: opacity 0.3s ease, transform 0.3s ease;
    z-index: 50;
  }

  .nav-btn:hover {
    opacity: 0.8;
    transform: translateY(-50%) scale(1.1);
  }

  .nav-prev { left: 1rem; }
  .nav-next { right: 1rem; }

  .nav-btn svg {
    width: 1.5rem;
    height: 1.5rem;
  }

  /* ── Photo Frame ── */
  .photo-frame {
    max-height: 100%;
    max-width: calc(100% - 7rem);
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
  }

  .photo-frame.wide,
  .photo-frame.ultra-wide {
    max-width: calc(100% - 5rem);
  }

  .photo-frame.tall,
  .photo-frame.portrait {
    max-width: min(50vw, calc(100% - 7rem));
  }

  .main-image {
    display: block;
    max-height: 100%;
    max-width: 100%;
    object-fit: contain;
    border-radius: 2px;
    box-shadow:
      0 20px 60px rgba(0, 0, 0, 0.5),
      0 5px 15px rgba(0, 0, 0, 0.3);
    animation: develop 1.2s ease-out;
    transition: box-shadow 0.4s ease, transform 0.4s ease;
  }

  .photo-frame:hover .main-image {
    box-shadow:
      0 25px 70px rgba(0, 0, 0, 0.6),
      0 8px 20px rgba(0, 0, 0, 0.4);
    transform: scale(1.008);
  }

  /* Photo "developing" animation */
  @keyframes develop {
    0% {
      filter: brightness(1.5) saturate(0) contrast(1.1);
      opacity: 0;
    }
    30% {
      filter: brightness(1.15) saturate(0.4) contrast(1.05);
      opacity: 0.8;
    }
    100% {
      filter: brightness(1) saturate(1) contrast(1);
      opacity: 1;
    }
  }

  /* ── Info Bar ── */
  .info-bar {
    flex-shrink: 0;
    padding: 0.75rem 2rem;
    border-top: 1px solid rgba(232, 224, 208, 0.06);
    cursor: pointer;
    user-select: none;
  }

  .info-row {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 2rem;
  }

  .info-left {
    display: flex;
    align-items: baseline;
    gap: 1.25rem;
    min-width: 0;
  }

  .info-number {
    font-family: var(--font-mono);
    font-size: 0.7rem;
    color: var(--amber);
    letter-spacing: 0.15em;
    flex-shrink: 0;
  }

  .info-text {
    min-width: 0;
  }

  .info-title {
    font-family: var(--font-serif);
    font-size: 1.05rem;
    font-weight: 400;
    color: var(--paper);
    letter-spacing: 0.03em;
    line-height: 1.2;
  }

  .info-desc {
    font-size: 0.78rem;
    color: var(--text-muted);
    font-style: italic;
    margin-top: 0.1rem;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  .info-right {
    display: flex;
    align-items: center;
    gap: 0.6rem;
    flex-shrink: 0;
  }

  .info-meta {
    font-size: 0.72rem;
    color: var(--text-muted);
    letter-spacing: 0.04em;
  }

  .info-sep {
    color: rgba(232, 224, 208, 0.15);
    font-size: 0.7rem;
  }

  .details-btn {
    background: none;
    border: none;
    color: var(--text-muted);
    width: 1.25rem;
    height: 1.25rem;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: transform 0.3s ease, color 0.3s ease;
    padding: 0;
  }

  .details-btn:hover { color: var(--amber); }
  .details-btn.open { transform: rotate(180deg); color: var(--amber); }

  .details-btn svg {
    width: 100%;
    height: 100%;
  }

  .info-expanded {
    padding: 0.75rem 0 0.5rem 3rem;
  }

  .info-thoughts {
    font-family: var(--font-serif);
    font-size: 0.9rem;
    line-height: 1.6;
    color: var(--text-secondary);
    font-style: italic;
    border-left: 2px solid var(--amber);
    padding-left: 1rem;
    max-width: 560px;
    opacity: 0.8;
  }

  /* ── Film Strip ── */
  .film-strip {
    flex-shrink: 0;
    border-top: 1px solid rgba(232, 224, 208, 0.04);
    background: rgba(196, 149, 106, 0.02);
    padding: 8px 0;
    position: relative;
  }

  /* Fade edges */
  .film-strip::before,
  .film-strip::after {
    content: '';
    position: absolute;
    top: 0;
    bottom: 0;
    width: 4rem;
    z-index: 2;
    pointer-events: none;
  }

  .film-strip::before {
    left: 0;
    background: linear-gradient(to right, var(--bg), transparent);
  }

  .film-strip::after {
    right: 0;
    background: linear-gradient(to left, var(--bg), transparent);
  }

  .strip-scroll {
    display: flex;
    overflow-x: auto;
    gap: 4px;
    padding: 0 calc(50vw - 26px);
    scrollbar-width: none;
    -ms-overflow-style: none;
  }

  .strip-scroll::-webkit-scrollbar { display: none; }

  .strip-frame {
    flex-shrink: 0;
    width: 52px;
    height: 52px;
    border: 1px solid rgba(232, 224, 208, 0.06);
    border-radius: 1px;
    overflow: hidden;
    cursor: pointer;
    background: none;
    padding: 0;
    position: relative;
    opacity: 0.3;
    transition: all 0.3s ease;
  }

  .strip-frame:hover {
    opacity: 0.6;
    border-color: rgba(232, 224, 208, 0.15);
  }

  .strip-frame.active {
    opacity: 1;
    border-color: var(--amber);
    box-shadow: 0 0 15px rgba(196, 149, 106, 0.2);
  }

  .strip-frame img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    display: block;
  }

  .frame-num {
    position: absolute;
    bottom: 1px;
    right: 2px;
    font-family: var(--font-mono);
    font-size: 7px;
    color: rgba(232, 224, 208, 0.4);
    line-height: 1;
    pointer-events: none;
  }

  .strip-frame.active .frame-num {
    color: var(--amber);
  }

  /* ── Mobile ── */
  @media (max-width: 768px) {
    .viewer-main {
      padding: 0.5rem 0;
    }

    .nav-btn {
      width: 2rem;
      height: 3rem;
    }

    .nav-btn svg {
      width: 1.2rem;
      height: 1.2rem;
    }

    .nav-prev { left: 0.25rem; }
    .nav-next { right: 0.25rem; }

    .photo-frame,
    .photo-frame.tall,
    .photo-frame.portrait {
      max-width: calc(100% - 4rem);
    }

    .info-bar {
      padding: 0.5rem 1rem;
    }

    .info-row {
      flex-direction: column;
      align-items: flex-start;
      gap: 0.15rem;
    }

    .info-right {
      padding-left: 2.2rem;
    }

    .info-expanded {
      padding-left: 2.2rem;
    }

    .info-title {
      font-size: 0.95rem;
    }

    .strip-frame {
      width: 38px;
      height: 38px;
    }

    .strip-scroll {
      gap: 3px;
      padding: 0 calc(50vw - 19px);
    }

    .film-strip {
      padding: 6px 0;
    }

    .frame-num {
      font-size: 5px;
    }

    .close-btn {
      top: 0.75rem;
      left: 0.75rem;
      width: 2rem;
      height: 2rem;
    }

    .close-btn svg {
      width: 0.75rem;
      height: 0.75rem;
    }

    .top-right {
      top: 1rem;
      right: 1rem;
      gap: 0.75rem;
    }

    .counter {
      font-size: 0.6rem;
    }

    .about-link {
      font-size: 0.55rem;
    }
  }
</style>
