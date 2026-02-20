<script>
  import { onMount, createEventDispatcher } from "svelte";
  import { fade, fly, scale } from "svelte/transition";
  import { quintOut } from "svelte/easing";

  export let photo;
  export let currentIndex;
  export let total;
  export let hasInteracted = false;

  const dispatch = createEventDispatcher();

  let showInfo = false;
  let imageLoaded = false;
  let aspectRatio = "default";
  let isHovering = false;
  let showChevrons = false;
  let hoverTimeout;

  function handleMouseEnter() {
    isHovering = true;
    showInfo = true;
    if (!hasInteracted) {
      dispatch("interact");
    }

    // Show chevrons after a brief delay
    hoverTimeout = setTimeout(() => {
      if (isHovering) {
        showChevrons = true;
      }
    }, 800);
  }

  function handleMouseLeave() {
    isHovering = false;
    showInfo = false;
    clearTimeout(hoverTimeout);
  }

  function goNext() {
    if (currentIndex < total - 1) {
      dispatch("next");
      showInfo = false;
      // Keep chevrons visible for continuous navigation
    }
  }

  function goPrev() {
    if (currentIndex > 0) {
      dispatch("prev");
      showInfo = false;
      // Keep chevrons visible for continuous navigation
    }
  }

  function handleKeydown(e) {
    if (e.key === "ArrowRight") {
      goNext();
    } else if (e.key === "ArrowLeft") {
      goPrev();
    }
  }

  // Detect aspect ratio when photo changes
  function detectAspectRatio(src) {
    imageLoaded = false;
    aspectRatio = "default";
    const img = new Image();
    img.src = src;
    img.onload = () => {
      const ratio = img.naturalWidth / img.naturalHeight;

      if (ratio > 2) aspectRatio = "ultra-wide";
      else if (ratio > 1.5) aspectRatio = "wide";
      else if (ratio > 1.2) aspectRatio = "large";
      else if (ratio > 0.9 && ratio < 1.1) aspectRatio = "square";
      else if (ratio > 0.7) aspectRatio = "tall";
      else aspectRatio = "portrait";

      imageLoaded = true;
    };
  }

  // Reactive statement to detect aspect ratio when photo changes
  $: detectAspectRatio(photo.src);

  onMount(() => {
    window.addEventListener("keydown", handleKeydown);

    return () => {
      window.removeEventListener("keydown", handleKeydown);
      clearTimeout(hoverTimeout);
    };
  });
</script>

<div class="photo-viewer" in:fade={{ duration: 400 }}>
  <!-- Progress indicator -->
  <div class="progress-bar">
    <div
      class="progress-fill"
      style="width: {((currentIndex + 1) / total) * 100}%"
    ></div>
  </div>

  <!-- Counter -->
  <div class="photo-counter" in:fade={{ delay: 300 }}>
    {String(currentIndex + 1).padStart(2, "0")} / {String(total).padStart(
      2,
      "0",
    )}
  </div>

  <!-- Main photo display -->
  <div class="viewer-content">
    {#key photo.src}
      <!-- svelte-ignore a11y_no_static_element_interactions -->
      <div class="photo-wrapper">
        <div
          class="photo-stage {aspectRatio}"
          in:scale={{ duration: 800, easing: quintOut, start: 0.95 }}
          on:mouseenter={handleMouseEnter}
          on:mouseleave={handleMouseLeave}
        >
          <img src={photo.src} alt={photo.title} class="viewer-image" />

          {#if !showInfo}
            <div
              class="photo-details"
              transition:fly={{ y: 30, duration: 400 }}
            >
              <h2 class="detail-title">{photo.title}</h2>
              <p class="detail-description">{photo.description}</p>

              <blockquote class="detail-thoughts">
                "{photo.thoughts}"
              </blockquote>

              <div class="detail-meta">
                <div class="meta-item">
                  <span class="meta-label">Location</span>
                  <span class="meta-value">{photo.location}</span>
                </div>
                <div class="meta-item">
                  <span class="meta-label">Camera</span>
                  <span class="meta-value">{photo.camera}</span>
                </div>
              </div>
            </div>
          {/if}
        </div>

        {#if showChevrons}
          <div class="external-chevrons" transition:fade={{ duration: 300 }}>
            {#if currentIndex > 0}
              <button
                class="chevron-btn chevron-prev"
                on:click={goPrev}
                aria-label="Previous photo"
              >
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 16 16" preserveAspectRatio="none"
                  ><!-- Icon from Bootstrap Icons by The Bootstrap Authors - https://github.com/twbs/icons/blob/main/LICENSE.md --><path
                    fill="currentColor"
                    fill-rule="evenodd"
                    d="M9.224 1.553a.5.5 0 0 1 .223.67L6.56 8l2.888 5.776a.5.5 0 1 1-.894.448l-3-6a.5.5 0 0 1 0-.448l3-6a.5.5 0 0 1 .67-.223"
                  /></svg
                >
              </button>
            {/if}

            {#if currentIndex < total - 1}
              <button
                class="chevron-btn chevron-next"
                on:click={goNext}
                aria-label="Next photo"
              >
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 16 16" preserveAspectRatio="none"
                  ><!-- Icon from Bootstrap Icons by The Bootstrap Authors - https://github.com/twbs/icons/blob/main/LICENSE.md --><path
                    fill="currentColor"
                    fill-rule="evenodd"
                    d="M6.776 1.553a.5.5 0 0 1 .671.223l3 6a.5.5 0 0 1 0 .448l-3 6a.5.5 0 1 1-.894-.448L9.44 8L6.553 2.224a.5.5 0 0 1 .223-.671"
                  /></svg
                >
              </button>
            {/if}
          </div>
        {/if}
      </div>
    {/key}
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

  .progress-bar {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    height: 3px;
    background: rgba(255, 255, 255, 0.1);
    z-index: 1000;
  }

  .progress-fill {
    height: 100%;
    background: var(--highlight);
    transition: width 0.5s ease;
  }

  .photo-counter {
    position: fixed;
    top: 2rem;
    right: 2rem;
    font-family: var(--font-sans);
    font-size: 0.9rem;
    letter-spacing: 0.2em;
    color: var(--text-muted);
    z-index: 1000;
  }

  .viewer-content {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 4rem 2rem;
    overflow: hidden;
  }

  .photo-wrapper {
    position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .photo-stage {
    position: relative;
    max-width: 90vw;
    max-height: 75vh;
    cursor: pointer;
    border-radius: 4px;
    overflow: visible;
    box-shadow:
      0 20px 60px rgba(0, 0, 0, 0.4),
      0 8px 20px rgba(0, 0, 0, 0.3);
  }

  .photo-stage.wide {
    max-width: 85vw;
  }
  .photo-stage.ultra-wide {
    max-width: 85vw;
  }
  .photo-stage.tall {
    max-width: 50vw;
    max-height: 75vh;
  }
  .photo-stage.portrait {
    max-width: 40vw;
    max-height: 80vh;
  }
  .photo-stage.square {
    max-width: 60vw;
    max-height: 70vh;
  }

  .viewer-image {
    display: block;
    width: 100%;
    height: 100%;
    object-fit: contain;
    transition: transform 0.6s ease;
    border-radius: 4px;
  }

  .photo-stage:hover .viewer-image {
    transform: scale(1.02);
  }

  .external-chevrons {
    position: absolute;
    top: 50%;
    left: 0;
    right: 0;
    transform: translateY(-50%);
    pointer-events: none;
    width: calc(100% + 10rem);
    margin-left: -5rem;
    z-index: 100;
  }

  .chevron-btn {
    position: absolute;
    top: 0;
    transform: translateY(-25%);
    background: transparent;
    border: none;
    color: var(--accent);
    width: 3.5rem;
    height: 10rem;
    margin-inline: 0px;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: all 0.3s ease;
    pointer-events: all;
    opacity: 0.3;
  }

  .chevron-btn:hover {
    opacity: 1;
    color: var(--highlight);
    transform: scale(1.15) translateY(-25%);
  }

  .chevron-prev {
    left: 0;
  }

  .chevron-next {
    right: 0;
  }

  .chevron-btn svg {
    width: 100%;
    height: 100%;

    /* stretch this to take up all available space */
    display: block;
    pointer-events: none;
  }

  .photo-details {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    background: linear-gradient(
      to top,
      rgba(0, 0, 0, 0.98) 0%,
      rgba(0, 0, 0, 0.95) 70%,
      rgba(0, 0, 0, 0.85) 100%
    );
    backdrop-filter: blur(20px);
    padding: 2rem 2.5rem 2rem;
    color: var(--accent);
    border-radius: 0 0 4px 4px;
    z-index: 10;
  }

  .detail-title {
    font-family: var(--font-serif);
    font-size: 1.5rem;
    font-weight: 400;
    margin-bottom: 0.4rem;
    letter-spacing: 0.05em;
  }

  .detail-description {
    font-size: 0.9rem;
    color: var(--text-secondary);
    font-style: italic;
    margin-bottom: 1rem;
  }

  .detail-thoughts {
    font-family: var(--font-serif);
    font-size: 1rem;
    line-height: 1.6;
    color: var(--text-secondary);
    font-style: italic;
    margin: 1rem 0;
    padding-left: 1.5rem;
    border-left: 3px solid var(--highlight);
  }

  .detail-meta {
    display: flex;
    gap: 2rem;
    margin-top: 1rem;
    padding-top: 1rem;
    border-top: 1px solid rgba(255, 255, 255, 0.1);
  }

  .meta-item {
    display: flex;
    flex-direction: column;
    gap: 0.3rem;
  }

  .meta-label {
    font-size: 0.75rem;
    text-transform: uppercase;
    letter-spacing: 0.15em;
    color: var(--text-muted);
  }

  .meta-value {
    font-size: 0.9rem;
    color: var(--accent);
  }

  @media (max-width: 768px) {
    .viewer-content {
      padding: 3rem 1rem;
    }

    .photo-stage {
      max-width: 90vw !important;
      max-height: 60vh;
    }

    .external-chevrons {
      width: calc(100% + 6rem);
      margin-left: -3rem;
    }

    .chevron-btn {
      width: 2.5rem;
      height: 5rem;
    }

    .chevron-btn svg {
      width: 100%;
      height: 100%;
    }

    .photo-details {
      padding: 1.5rem 1.5rem 1.5rem;
    }

    .detail-title {
      font-size: 1.2rem;
    }

    .detail-thoughts {
      font-size: 0.9rem;
      line-height: 1.5;
    }

    .detail-meta {
      flex-direction: column;
      gap: 0.75rem;
    }
  }
</style>
