<script lang="ts">
  import { asset, resolve } from '$app/paths';
  import ThemeToggle from '$lib/components/ThemeToggle.svelte';
</script>

<svelte:head>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin="anonymous">
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;700&family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@24,400,0,0&display=swap" rel="stylesheet">
  <link href="https://fonts.googleapis.com/css2?family=Inter:ital,opsz,wght@0,14..32,100..900;1,14..32,100..900&family=Space+Grotesk:wght@300..700&display=swap" rel="stylesheet">
</svelte:head>

<div class="topbar">
  <div class="brand">
    <a class="brand-icon-link" href={resolve('/')} aria-label="Stasis home">
      <img src={asset('/favicon.png')} alt="" class="brand-icon" />
    </a>
    <span><a class="title" href={resolve('/')}>Stasis</a></span>
  </div>
  <nav>
    <ul>
      <li><a href={resolve('/quick-start')}>Quick Start</a></li>
      <li><a href={resolve('/configuration')}>Configuration</a></li>
      <li><a href={resolve('/integration')}>Integration</a></li>
      <li><a href={resolve('/contributing')}>Contributing</a></li>
      <li><a href={resolve('/faq')}>FAQ</a></li>
    </ul>
  </nav>
  <div class="links">
    <ThemeToggle />
    <a class="github" href="https://github.com/saltnpepper97/stasis" aria-label="View on GitHub">
      <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="currentColor">
        <path d="M12 0c-6.626 0-12 5.373-12 12 0 5.302 3.438 9.8 8.207 11.387.599.111.793-.261.793-.577v-2.234c-3.338.726-4.033-1.416-4.033-1.416-.546-1.387-1.333-1.756-1.333-1.756-1.089-.745.083-.729.083-.729 1.205.084 1.839 1.237 1.839 1.237 1.07 1.834 2.807 1.304 3.492.997.107-.775.418-1.305.762-1.604-2.665-.305-5.467-1.334-5.467-5.931 0-1.311.469-2.381 1.236-3.221-.124-.303-.535-1.524.117-3.176 0 0 1.008-.322 3.301 1.23.957-.266 1.983-.399 3.003-.404 1.02.005 2.047.138 3.006.404 2.291-1.552 3.297-1.23 3.297-1.23.653 1.653.242 2.874.118 3.176.77.84 1.235 1.911 1.235 3.221 0 4.609-2.807 5.624-5.479 5.921.43.372.823 1.102.823 2.222v3.293c0 .319.192.694.801.576 4.765-1.589 8.199-6.086 8.199-11.386 0-6.627-5.373-12-12-12z"/>
      </svg>
    </a>
  </div>
</div>

<style>
/* Default title style (outside topbar, if ever reused) */
.title {
  font-family: 'Space Grotesk', sans-serif;
  font-size: 28px;
  text-decoration: none;
  color: var(--text-primary);
  letter-spacing: 0.5px;
}

.topbar {
  position: fixed;
  top: 0;
  width: 100%;
  z-index: 100;
  display: grid;
  grid-template-columns: auto 1fr auto;
  align-items: center;
  background:
    linear-gradient(135deg,
      color-mix(in srgb, var(--gradient-start) 32%, transparent),
      color-mix(in srgb, var(--gradient-end) 28%, transparent)),
    color-mix(in srgb, var(--bg-primary) 78%, transparent);
  padding: 12px 24px;
  border-bottom: 1px solid color-mix(in srgb, var(--accent) 18%, transparent);
  box-shadow: 0 14px 40px rgba(0, 0, 0, 0.18);
  backdrop-filter: blur(18px) saturate(135%);
  gap: 32px;
}

/* ===== Topbar text should always be white (both themes) ===== */
.topbar .title {
  color: #fff;
}

.topbar .brand {
  display: flex;
  align-items: center;
  gap: 12px;
  font-weight: 700;
  font-size: 1.1rem;
  color: #fff;
  white-space: nowrap;
}

.brand-icon {
  width: 34px;
  height: 34px;
  object-fit: contain;
  flex: 0 0 auto;
  filter:
    drop-shadow(0 2px 5px rgba(52, 232, 226, 0.25))
    drop-shadow(0 1px 4px rgba(75, 53, 245, 0.18));
}

.brand-icon-link {
  display: flex;
  align-items: center;
  justify-content: center;
  line-height: 0;
  border-radius: 8px;
}

.brand-icon-link:focus-visible {
  outline: 2px solid rgba(255, 255, 255, 0.85);
  outline-offset: 3px;
}

nav {
  display: flex;
  justify-content: center;
}

ul {
  display: flex;
  list-style: none;
  gap: 8px;
  margin: 0;
  padding: 0;
}

li {
  display: flex;
  align-items: center;
}

/* Links always white-ish on the gradient */
nav a {
  display: flex;
  text-decoration: none;
  align-items: center;
  justify-content: center;
  color: rgba(255, 255, 255, 0.88);
  padding: 8px 16px;
  border-radius: 999px;
  font-weight: 500;
  font-size: 0.95rem;
  transition: all 0.2s ease;
  position: relative;
}

nav a:hover {
  background-color: rgba(244, 251, 255, 0.13);
  color: #fff;
}

nav a::after {
  content: '';
  position: absolute;
  bottom: 2px;
  left: 50%;
  transform: translateX(-50%) scaleX(0);
  width: min(56%, 36px);
  height: 1px;
  background: rgba(131, 242, 255, 0.9);
  transition: transform 0.2s ease;
}

nav a:hover::after {
  transform: translateX(-50%) scaleX(1);
}

.links {
  display: flex;
  gap: 12px;
}

/* ===== GitHub: always black icon (both themes) ===== */
.github {
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: rgba(244, 251, 255, 0.12);
  padding: 8px;
  border-radius: 50%;
  transition: all 0.2s ease;
  backdrop-filter: blur(10px);
  border: 1px solid rgba(244, 251, 255, 0.18);
  color: #000; /* ALWAYS black */
}

.github:hover {
  background-color: rgba(244, 251, 255, 0.20);
  transform: scale(1.1);
}

.github svg {
  display: block;
}

@media (max-width: 768px) {
  .topbar {
    grid-template-columns: 1fr;
    gap: 16px;
    padding: 16px;
  }

  .brand {
    justify-content: center;
  }

  nav {
    overflow-x: auto;
  }

  ul {
    gap: 4px;
  }

  nav a {
    padding: 6px 12px;
    font-size: 0.9rem;
  }

  .links {
    justify-content: center;
  }
}
</style>
