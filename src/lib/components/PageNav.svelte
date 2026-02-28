<script lang="ts">
  import { page } from '$app/state';
  import { base } from '$app/paths';

  const pages = [
    { label: 'Home',          href: `${base}/` },
    { label: 'Quick Start',   href: `${base}/quick-start` },
    { label: 'Configuration', href: `${base}/configuration` },
    { label: 'Integration',   href: `${base}/integration` },
    { label: 'Contributing',  href: `${base}/contributing` },
    { label: 'FAQ',           href: `${base}/faq` },
  ];

  const currentIndex = $derived(
    pages.findIndex(p => p.href === ((page.url?.pathname ?? '/').replace(/\/$/, '') || '/'))
  );

  const prev = $derived(currentIndex > 0 ? pages[currentIndex - 1] : null);
  const next = $derived(currentIndex < pages.length - 1 ? pages[currentIndex + 1] : null);
</script>

<nav class="page-nav">
  <!-- Desktop layout -->
  <div class="nav-inner">
    {#if prev}
      <a href={prev.href} class="nav-btn prev">
        <span class="arrow">←</span>
        <span class="nav-text">
          <span class="nav-label">Previous</span>
          <span class="nav-title">{prev.label}</span>
        </span>
      </a>
    {:else}
      <div></div>
    {/if}

    <div class="page-dots">
      {#each pages as p, i}
        <a
          href={p.href}
          class="dot"
          class:active={i === currentIndex}
          title={p.label}
          aria-label={p.label}
        ></a>
      {/each}
    </div>

    {#if next}
      <a href={next.href} class="nav-btn next">
        <span class="nav-text">
          <span class="nav-label">Next</span>
          <span class="nav-title">{next.label}</span>
        </span>
        <span class="arrow">→</span>
      </a>
    {:else}
      <div></div>
    {/if}
  </div>

  <!-- Mobile layout: full-width stacked buttons + centered dots -->
  <div class="nav-mobile">
    {#if prev}
      <a href={prev.href} class="nav-btn-mobile prev">
        <span class="arrow">←</span>
        <span class="nav-text">
          <span class="nav-label">Previous</span>
          <span class="nav-title">{prev.label}</span>
        </span>
      </a>
    {/if}
    {#if next}
      <a href={next.href} class="nav-btn-mobile next">
        <span class="nav-text">
          <span class="nav-label">Next</span>
          <span class="nav-title">{next.label}</span>
        </span>
        <span class="arrow">→</span>
      </a>
    {/if}
    <div class="page-dots-mobile">
      {#each pages as p, i}
        <a
          href={p.href}
          class="dot"
          class:active={i === currentIndex}
          title={p.label}
          aria-label={p.label}
        ></a>
      {/each}
    </div>
  </div>
</nav>

<style>
  .page-nav {
    width: 100%;
    padding: 2rem 0 1rem;
    margin-top: 3rem;
    border-top: 1px solid var(--border-color);
  }

  /* ── Desktop ── */
  .nav-inner {
    display: grid;
    grid-template-columns: 1fr auto 1fr;
    align-items: center;
    gap: 1rem;
  }

  .nav-btn {
    display: flex;
    align-items: center;
    gap: 0.75rem;
    padding: 0.875rem 1.25rem;
    border-radius: 10px;
    text-decoration: none;
    border: 1px solid var(--border-color);
    background: var(--bg-secondary);
    color: var(--text-primary);
    transition: border-color 0.2s ease, transform 0.2s ease, box-shadow 0.2s ease;
    max-width: 220px;
  }

  .nav-btn:hover {
    border-color: var(--accent);
    transform: translateY(-2px);
    box-shadow: 0 4px 16px color-mix(in srgb, var(--accent) 12%, transparent);
  }

  .nav-btn.next {
    justify-self: end;
    justify-content: flex-end;
    text-align: right;
  }

  /* ── Shared text ── */
  .nav-text {
    display: flex;
    flex-direction: column;
    gap: 0.1rem;
  }

  .nav-label {
    font-size: 0.7rem;
    font-weight: 500;
    color: var(--text-secondary);
    text-transform: uppercase;
    letter-spacing: 0.08em;
  }

  .nav-title {
    font-size: 0.95rem;
    font-weight: 600;
    color: var(--text-primary);
  }

  .arrow {
    font-size: 1.1rem;
    color: var(--accent);
    flex-shrink: 0;
    transition: transform 0.2s ease;
  }

  .nav-btn.prev:hover .arrow { transform: translateX(-3px); }
  .nav-btn.next:hover .arrow { transform: translateX(3px); }

  /* ── Dots ── */
  .page-dots {
    display: flex;
    align-items: center;
    gap: 6px;
  }

  .dot {
    width: 7px;
    height: 7px;
    border-radius: 50%;
    background: var(--border-color);
    border: 1px solid var(--border-color);
    transition: background 0.2s ease, transform 0.2s ease;
    flex-shrink: 0;
  }

  .dot:hover {
    background: var(--text-secondary);
    transform: scale(1.3);
  }

  .dot.active {
    background: var(--accent);
    border-color: var(--accent);
    transform: scale(1.3);
  }

  /* ── Mobile (hidden on desktop) ── */
  .nav-mobile {
    display: none;
  }

  @media (max-width: 600px) {
    .nav-inner {
      display: none;
    }

    .nav-mobile {
      display: flex;
      flex-direction: column;
      gap: 0.75rem;
    }

    .nav-btn-mobile {
      display: flex;
      align-items: center;
      gap: 0.75rem;
      padding: 0.875rem 1.125rem;
      border-radius: 10px;
      text-decoration: none;
      border: 1px solid var(--border-color);
      background: var(--bg-secondary);
      color: var(--text-primary);
      transition: border-color 0.2s ease, box-shadow 0.2s ease;
      width: 100%;
    }

    .nav-btn-mobile:hover {
      border-color: var(--accent);
      box-shadow: 0 4px 16px color-mix(in srgb, var(--accent) 12%, transparent);
    }

    .nav-btn-mobile.next {
      justify-content: space-between;
      text-align: right;
    }

    .nav-btn-mobile.prev:hover .arrow { transform: translateX(-3px); }
    .nav-btn-mobile.next:hover .arrow { transform: translateX(3px); }

    .page-dots-mobile {
      display: flex;
      justify-content: center;
      gap: 8px;
      padding-top: 0.5rem;
    }

    .page-dots-mobile .dot {
      width: 8px;
      height: 8px;
    }
  }
</style>
