<script>
  import { createEventDispatcher } from 'svelte';
  import { fade, fly } from 'svelte/transition';

  const dispatch = createEventDispatcher();
</script>

<section class="hero">
  <!-- Ambient safelight glow -->
  <div class="ambient-glow"></div>

  <!-- Registration marks -->
  <div class="reg-marks">
    <div class="mark mark-tl"></div>
    <div class="mark mark-tr"></div>
    <div class="mark mark-bl"></div>
    <div class="mark mark-br"></div>
  </div>

  <!-- Film edge identifier -->
  <div class="film-edge" in:fade={{ duration: 800, delay: 1200 }}>
    ROLL 001 · 20 EXP · 35mm
  </div>

  <!-- Main content -->
  <div class="hero-center">
    <!-- Name -->
    <div class="hero-name">
      <div class="name-line">
        <span class="char" style="animation-delay: 0.15s">Z</span><span class="char" style="animation-delay: 0.22s">a</span><span class="char" style="animation-delay: 0.29s">c</span><span class="char" style="animation-delay: 0.36s">h</span>
      </div>
      <div class="name-line name-line-offset">
        <span class="char" style="animation-delay: 0.5s">L</span><span class="char" style="animation-delay: 0.57s">u</span><span class="char" style="animation-delay: 0.64s">c</span><span class="char" style="animation-delay: 0.71s">a</span><span class="char" style="animation-delay: 0.78s">s</span>
      </div>
    </div>

    <!-- Decorative rule -->
    <div class="hero-rule" in:fly={{ y: 8, duration: 600, delay: 950 }}>
      <div class="rule-line"></div>
      <div class="rule-diamond"></div>
      <div class="rule-line"></div>
    </div>

    <!-- Tagline -->
    <p class="hero-tagline" in:fade={{ duration: 800, delay: 1050 }}>
      Fine Art Photography
    </p>

    <!-- Enter -->
    <button class="hero-enter" on:click={() => dispatch('start')} in:fade={{ duration: 800, delay: 1250 }}>
      <span class="enter-label">Enter Gallery</span>
      <span class="enter-arrow">
        <svg viewBox="0 0 32 12" fill="none">
          <line x1="0" y1="6" x2="26" y2="6" stroke="currentColor" stroke-width="1"/>
          <polyline points="20,1 26,6 20,11" fill="none" stroke="currentColor" stroke-width="1"/>
        </svg>
      </span>
    </button>

    <!-- Bottom row -->
    <div class="hero-bottom" in:fade={{ duration: 600, delay: 1500 }}>
      <p class="hero-count">20 Photographs</p>
      <button class="about-link" on:click={() => dispatch('about')}>About</button>
    </div>
  </div>
</section>

<style>
  .hero {
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    position: relative;
    overflow: hidden;
  }

  /* Subtle amber glow — like a darkroom safelight */
  .ambient-glow {
    position: absolute;
    inset: 0;
    background: radial-gradient(
      ellipse 70% 55% at 50% 42%,
      rgba(196, 149, 106, 0.045) 0%,
      transparent 100%
    );
    animation: glowPulse 8s ease-in-out infinite alternate;
    pointer-events: none;
  }

  @keyframes glowPulse {
    from { opacity: 0.4; transform: scale(0.96); }
    to { opacity: 1; transform: scale(1.04); }
  }

  /* Corner registration marks */
  .reg-marks {
    position: absolute;
    inset: 2.5rem;
    pointer-events: none;
  }

  .mark {
    position: absolute;
    width: 20px;
    height: 20px;
  }

  .mark-tl {
    top: 0; left: 0;
    border-top: 1px solid rgba(232, 224, 208, 0.15);
    border-left: 1px solid rgba(232, 224, 208, 0.15);
  }
  .mark-tr {
    top: 0; right: 0;
    border-top: 1px solid rgba(232, 224, 208, 0.15);
    border-right: 1px solid rgba(232, 224, 208, 0.15);
  }
  .mark-bl {
    bottom: 0; left: 0;
    border-bottom: 1px solid rgba(232, 224, 208, 0.15);
    border-left: 1px solid rgba(232, 224, 208, 0.15);
  }
  .mark-br {
    bottom: 0; right: 0;
    border-bottom: 1px solid rgba(232, 224, 208, 0.15);
    border-right: 1px solid rgba(232, 224, 208, 0.15);
  }

  /* Film edge text */
  .film-edge {
    position: absolute;
    top: 2.5rem;
    right: 3.5rem;
    font-family: var(--font-mono);
    font-size: 0.6rem;
    letter-spacing: 0.2em;
    color: rgba(232, 224, 208, 0.18);
    text-transform: uppercase;
  }

  /* Center content */
  .hero-center {
    text-align: center;
    position: relative;
    z-index: 1;
  }

  /* Name typography */
  .hero-name {
    font-family: var(--font-serif);
    font-size: clamp(4.5rem, 14vw, 11rem);
    font-weight: 400;
    letter-spacing: 0.12em;
    line-height: 0.92;
    color: var(--paper);
  }

  .name-line {
    display: block;
    white-space: nowrap;
  }

  .name-line-offset {
    margin-left: clamp(1rem, 4vw, 3rem);
  }

  /* Letter-by-letter reveal */
  .char {
    display: inline-block;
    animation: charIn 0.9s cubic-bezier(0.16, 1, 0.3, 1) both;
  }

  @keyframes charIn {
    from {
      opacity: 0;
      transform: translateY(40px);
      filter: blur(10px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
      filter: blur(0);
    }
  }

  /* Decorative rule with diamond */
  .hero-rule {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 0.75rem;
    margin: 2.5rem 0 1.5rem;
  }

  .rule-line {
    width: 2.5rem;
    height: 1px;
    background: rgba(232, 224, 208, 0.2);
  }

  .rule-diamond {
    width: 5px;
    height: 5px;
    border: 1px solid var(--amber);
    transform: rotate(45deg);
    opacity: 0.8;
  }

  /* Tagline */
  .hero-tagline {
    font-family: var(--font-sans);
    font-size: 0.8rem;
    letter-spacing: 0.3em;
    text-transform: uppercase;
    color: rgba(232, 224, 208, 0.4);
    font-weight: 400;
  }

  /* Enter button */
  .hero-enter {
    margin-top: 3rem;
    display: inline-flex;
    align-items: center;
    gap: 1.2rem;
    background: transparent;
    border: 1px solid rgba(232, 224, 208, 0.2);
    color: var(--paper);
    padding: 0.9rem 2.2rem;
    font-family: var(--font-sans);
    font-size: 0.8rem;
    letter-spacing: 0.2em;
    text-transform: uppercase;
    cursor: pointer;
    transition: all 0.5s ease;
    position: relative;
    overflow: hidden;
  }

  .hero-enter::before {
    content: '';
    position: absolute;
    inset: 0;
    background: var(--amber);
    transform: scaleX(0);
    transform-origin: left;
    transition: transform 0.5s cubic-bezier(0.16, 1, 0.3, 1);
    z-index: -1;
  }

  .hero-enter:hover {
    color: var(--bg);
    border-color: var(--amber);
  }

  .hero-enter:hover::before {
    transform: scaleX(1);
  }

  .enter-arrow {
    display: flex;
    align-items: center;
  }

  .enter-arrow svg {
    width: 1.8rem;
    height: 0.6rem;
    transition: transform 0.3s ease;
  }

  .hero-enter:hover .enter-arrow svg {
    transform: translateX(5px);
  }

  /* Bottom row */
  .hero-bottom {
    margin-top: 2.5rem;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 1.5rem;
  }

  .hero-count {
    font-family: var(--font-mono);
    font-size: 0.6rem;
    letter-spacing: 0.25em;
    color: rgba(232, 224, 208, 0.18);
    text-transform: uppercase;
  }

  .about-link {
    font-family: var(--font-mono);
    font-size: 0.6rem;
    letter-spacing: 0.25em;
    color: rgba(232, 224, 208, 0.18);
    text-transform: uppercase;
    background: none;
    border: none;
    cursor: pointer;
    padding: 0;
    transition: color 0.3s ease;
    position: relative;
  }

  .about-link::before {
    content: '·';
    position: absolute;
    left: -0.9rem;
    color: rgba(232, 224, 208, 0.12);
  }

  .about-link:hover {
    color: var(--amber);
  }

  /* === Mobile === */
  @media (max-width: 768px) {
    .hero-name {
      font-size: clamp(3rem, 12vw, 6rem);
      letter-spacing: 0.08em;
    }

    .name-line-offset {
      margin-left: clamp(0.5rem, 2vw, 1.5rem);
    }

    .reg-marks {
      inset: 1.5rem;
    }

    .film-edge {
      top: 1.5rem;
      right: 2rem;
      font-size: 0.5rem;
    }

    .hero-enter {
      padding: 0.75rem 1.8rem;
      font-size: 0.7rem;
    }

    .hero-rule {
      margin: 2rem 0 1.2rem;
    }
  }
</style>
