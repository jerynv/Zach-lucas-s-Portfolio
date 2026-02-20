<script>
  import { createEventDispatcher } from 'svelte';
  import { fade, fly } from 'svelte/transition';

  const dispatch = createEventDispatcher();

  function close() {
    dispatch('close');
  }

  function handleKeydown(e) {
    if (e.key === 'Escape') close();
  }
</script>

<svelte:window on:keydown={handleKeydown} />

<!-- svelte-ignore a11y_click_events_have_key_events -->
<!-- svelte-ignore a11y_no_static_element_interactions -->
<div class="about-overlay" on:click={close} transition:fade={{ duration: 400 }}>
  <!-- svelte-ignore a11y_click_events_have_key_events -->
  <!-- svelte-ignore a11y_no_static_element_interactions -->
  <div class="about-panel" on:click|stopPropagation transition:fly={{ y: 30, duration: 500, delay: 100 }}>
    <!-- Close -->
    <button class="about-close" on:click={close} aria-label="Close">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
        <line x1="6" y1="6" x2="18" y2="18"/>
        <line x1="6" y1="18" x2="18" y2="6"/>
      </svg>
    </button>

    <!-- Corner marks -->
    <div class="mark mark-tl"></div>
    <div class="mark mark-tr"></div>
    <div class="mark mark-bl"></div>
    <div class="mark mark-br"></div>

    <!-- Content -->
    <div class="about-content">
      <p class="about-label">About</p>

      <h2 class="about-name">Zach Lucas</h2>

      <div class="about-rule">
        <div class="rule-line"></div>
        <div class="rule-diamond"></div>
        <div class="rule-line"></div>
      </div>

      <div class="about-body">
        <p>
          I see art in everything. In the way light falls across a weathered wall.
          In the geometry of a stranger's silhouette against a city skyline. In the
          quiet moments most people walk past without noticing.
        </p>

        <p>
          Photography isn't just something I do — it's how I understand the world.
          Every frame is an attempt to hold onto something fleeting, to show others
          what I see when I slow down and really look.
        </p>

        <p>
          I shoot primarily on film because the process demands patience. There's no
          instant preview, no endless retakes. Each exposure is a commitment — a
          conversation between me, the light, and whatever truth the moment holds.
        </p>

        <p>
          This portfolio is a collection of those conversations. Some loud, some
          whispered. All honest.
        </p>
      </div>

      <p class="about-sign">— Z.L.</p>
    </div>
  </div>
</div>

<style>
  .about-overlay {
    position: fixed;
    inset: 0;
    z-index: 500;
    background: rgba(10, 10, 10, 0.85);
    backdrop-filter: blur(12px);
    -webkit-backdrop-filter: blur(12px);
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 2rem;
  }

  .about-panel {
    position: relative;
    max-width: 560px;
    width: 100%;
    padding: 3.5rem 3rem;
    background: var(--bg);
    border: 1px solid rgba(232, 224, 208, 0.08);
  }

  /* Corner marks */
  .mark {
    position: absolute;
    width: 14px;
    height: 14px;
  }

  .mark-tl {
    top: -1px; left: -1px;
    border-top: 1px solid var(--amber);
    border-left: 1px solid var(--amber);
  }
  .mark-tr {
    top: -1px; right: -1px;
    border-top: 1px solid var(--amber);
    border-right: 1px solid var(--amber);
  }
  .mark-bl {
    bottom: -1px; left: -1px;
    border-bottom: 1px solid var(--amber);
    border-left: 1px solid var(--amber);
  }
  .mark-br {
    bottom: -1px; right: -1px;
    border-bottom: 1px solid var(--amber);
    border-right: 1px solid var(--amber);
  }

  /* Close */
  .about-close {
    position: absolute;
    top: 1.25rem;
    right: 1.25rem;
    width: 2rem;
    height: 2rem;
    background: none;
    border: none;
    color: var(--text-muted);
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: color 0.3s ease;
  }

  .about-close:hover {
    color: var(--amber);
  }

  .about-close svg {
    width: 0.85rem;
    height: 0.85rem;
  }

  /* Content */
  .about-content {
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
  }

  .about-label {
    font-family: var(--font-mono);
    font-size: 0.6rem;
    letter-spacing: 0.3em;
    text-transform: uppercase;
    color: var(--amber);
    margin-bottom: 1.5rem;
  }

  .about-name {
    font-family: var(--font-serif);
    font-size: clamp(2rem, 5vw, 2.8rem);
    font-weight: 400;
    letter-spacing: 0.08em;
    color: var(--paper);
    line-height: 1;
  }

  .about-rule {
    display: flex;
    align-items: center;
    gap: 0.6rem;
    margin: 1.75rem 0;
  }

  .rule-line {
    width: 2rem;
    height: 1px;
    background: rgba(232, 224, 208, 0.15);
  }

  .rule-diamond {
    width: 4px;
    height: 4px;
    border: 1px solid var(--amber);
    transform: rotate(45deg);
    opacity: 0.7;
  }

  .about-body {
    text-align: left;
    display: flex;
    flex-direction: column;
    gap: 1.1rem;
  }

  .about-body p {
    font-family: var(--font-serif);
    font-size: 1rem;
    line-height: 1.75;
    color: var(--text-secondary);
    font-style: italic;
  }

  .about-body p:first-child {
    color: var(--paper);
    font-style: normal;
    font-size: 1.05rem;
  }

  .about-sign {
    margin-top: 2rem;
    font-family: var(--font-serif);
    font-size: 1rem;
    color: var(--amber);
    letter-spacing: 0.05em;
    opacity: 0.8;
  }

  /* Mobile */
  @media (max-width: 768px) {
    .about-panel {
      padding: 2.5rem 1.75rem;
    }

    .about-body p {
      font-size: 0.92rem;
    }
  }
</style>
