<script lang="ts">
  import { onMount } from 'svelte';

  type Theme = 'auto' | 'light' | 'dark';

  let theme: Theme = 'auto';
  const themeChangeEvent = 'stasis-theme-change';

  onMount(() => {
    theme = getStoredTheme();

    applyTheme();

    const mediaQuery = window.matchMedia('(prefers-color-scheme: dark)');
    const handler = () => {
      if (theme === 'auto') applyTheme();
    };

    mediaQuery.addEventListener('change', handler);
    window.addEventListener(themeChangeEvent, syncTheme);
    window.addEventListener('storage', syncTheme);

    return () => {
      mediaQuery.removeEventListener('change', handler);
      window.removeEventListener(themeChangeEvent, syncTheme);
      window.removeEventListener('storage', syncTheme);
    };
  });

  function getStoredTheme(): Theme {
    const savedTheme = localStorage.getItem('theme');

    if (savedTheme === 'auto' || savedTheme === 'light' || savedTheme === 'dark') {
      return savedTheme;
    }

    return 'auto';
  }

  function syncTheme() {
    theme = getStoredTheme();
    applyTheme();
  }

  function toggleTheme() {
    if (theme === 'auto') {
      theme = 'light';
    } else if (theme === 'light') {
      theme = 'dark';
    } else {
      theme = 'auto';
    }

    applyTheme();
    localStorage.setItem('theme', theme);
    window.dispatchEvent(new CustomEvent(themeChangeEvent));
  }

  function applyTheme() {
    const effectiveTheme = theme === 'auto'
      ? window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light'
      : theme;

    document.documentElement.setAttribute('data-theme', effectiveTheme);
  }

  function getThemeIcon() {
    switch (theme) {
      case 'auto': return 'brightness_auto';
      case 'light': return 'light_mode';
      case 'dark': return 'dark_mode';
    }
  }
</script>

<button class="theme-toggle" onclick={toggleTheme} aria-label="Toggle theme: {theme}" title="Theme: {theme}">
  <span class="material-symbols-outlined">
    {getThemeIcon()}
  </span>
</button>

<style>
  .material-symbols-outlined {
    font-family: 'Material Symbols Outlined';
    font-weight: normal;
    font-style: normal;
    font-size: 24px;
    line-height: 1;
    letter-spacing: normal;
    text-transform: none;
    display: inline-block;
    white-space: nowrap;
    word-wrap: normal;
    direction: ltr;
    -webkit-font-smoothing: antialiased;
  }

  .theme-toggle {
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: rgba(244, 251, 255, 0.12);
    padding: 8px;
    border-radius: 50%;
    border: 1px solid rgba(244, 251, 255, 0.18);
    cursor: pointer;
    transition: all 0.2s ease;
    backdrop-filter: blur(10px);
  }

  :global([data-theme="dark"]) .theme-toggle {
    color: #fff;
  }

  :global([data-theme="light"]) .theme-toggle {
    color: #111;
  }

  .theme-toggle:hover {
    background-color: rgba(244, 251, 255, 0.20);
    transform: scale(1.1);
  }
</style>
