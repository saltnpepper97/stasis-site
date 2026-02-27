<script lang="ts">
  import { onMount } from 'svelte';
  import CodeBlock from '$lib/components/CodeBlock.svelte';
  
  let activeSection = $state('');
  
  const sections = [
    { id: 'quickshell', title: 'Quickshell' },
    { id: 'waybar', title: 'Waybar' }
  ];
  
  onMount(() => {
    const observer = new IntersectionObserver(
      (entries) => {
        entries.forEach((entry) => {
          if (entry.isIntersecting) {
            activeSection = entry.target.id;
          }
        });
      },
      { rootMargin: '-100px 0px -80% 0px' }
    );
    
    sections.forEach(({ id }) => {
      const element = document.getElementById(id);
      if (element) observer.observe(element);
    });
    
    return () => observer.disconnect();
  });
  
  function scrollToSection(id: string) {
    const element = document.getElementById(id);
    if (!element) return;

    const topbarHeight = 70;
    const elementPosition = element.getBoundingClientRect().top + window.scrollY;
    const offsetPosition = elementPosition - topbarHeight;
    
    window.scrollTo({
      top: offsetPosition,
      behavior: 'smooth'
    });
  }
  
  // Waybar examples
  const waybarIconCode = `"custom/stasis": {
  "exec": "stasis info --json",
  "format": "{icon}",
  "format-icons": {
    "idle_active": "",
    "idle_inhibited": "",
    "manually_inhibited": "",
    "not_running": "󰒲"
  },
  "tooltip": true,
  "on-click": "stasis toggle-inhibit",
  "interval": 2,
  "restart-interval": 2,
  "return-type": "json"
}`;

  const waybarTextCode = `"custom/stasis": {
  "exec": "stasis info --json",
  "format": "{text}",
  "tooltip": true,
  "on-click": "stasis toggle-inhibit",
  "interval": 2,
  "restart-interval": 2,
  "return-type": "json"
}`;
</script>

<div class="page-container">
  <nav class="links-nav">
    <div class="nav-title">On this page</div>
    <ul>
      {#each sections as section}
        <li>
          <button
            type="button"
            class:active={activeSection === section.id}
            onclick={() => scrollToSection(section.id)}
          >
            {section.title}
          </button>
        </li>
      {/each}
    </ul>
  </nav>
  
  <main class="content">
    <h1>Integration</h1>

    <section id="quickshell">
      <h2>Quickshell</h2>

      <p>
        Quickshell integration is pending upstream support.
      </p>
    </section>
    
    <section id="waybar">
      <h2>Waybar</h2>
      
      <h3>Example Custom Module</h3>
      <p>
        To use Stasis with Waybar, define a custom module that polls
        <code>stasis info --json</code>.
      </p>
      
      <h4>Icon-based Display</h4>
      <CodeBlock code={waybarIconCode} language="json" />

      <h4>Text-based Display</h4>
      <p>Or display text instead of icons:</p>
      <CodeBlock code={waybarTextCode} language="json" />
    </section>
  </main>
</div>

<style>
/* === LAYOUT === */
.page-container {
  display: grid;
  grid-template-columns: 200px 1fr;
  gap: 40px;
  max-width: 1200px;
  margin: 0 auto;
  padding: 40px 20px;
}

/* === SIDE NAV === */
.links-nav {
  position: sticky;
  top: 80px;
  height: fit-content;
}

.nav-title {
  font-weight: 600;
  font-size: 0.85rem;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  color: var(--text-secondary);
  margin-bottom: 12px;
}

.links-nav ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.links-nav li {
  margin: 0;
}

.links-nav button {
  display: block;
  width: 100%;
  text-align: left;
  background: none;
  border: none;
  border-left: 2px solid var(--border-color);
  padding: 6px 0 6px 16px;
  font-size: 0.9rem;
  color: var(--text-secondary);
  cursor: pointer;
  transition: all 0.2s ease;
}

.links-nav button:hover {
  color: var(--text-primary);
  border-left-color: var(--accent);
}

.links-nav button.active {
  color: var(--accent);
  border-left-color: var(--accent);
  font-weight: 500;
}

/* === CONTENT === */
.content {
  min-width: 0;
}

h1 {
  font-size: 2.5rem;
  font-weight: 700;
  margin: 0 0 32px 0;
  color: var(--text-primary);
}

h2 {
  font-size: 1.75rem;
  font-weight: 600;
  margin: 48px 0 16px 0;
  color: var(--text-primary);
  scroll-margin-top: 120px;
}

h3 {
  font-size: 1.4rem;
  font-weight: 600;
  margin: 32px 0 12px 0;
  color: var(--text-primary);
}

h4 {
  font-size: 1.1rem;
  font-weight: 600;
  margin: 24px 0 12px 0;
  color: var(--text-primary);
}

section {
  margin-bottom: 48px;
  scroll-margin-top: 120px;
}

p {
  line-height: 1.7;
  color: var(--text-primary);
  margin: 16px 0;
}

/* === MOBILE === */
@media (max-width: 768px) {
  .page-container {
    grid-template-columns: 1fr;
    gap: 20px;
    padding: 80px 16px 20px;
  }

  .links-nav {
    position: static;
    border-bottom: 1px solid var(--border-color);
    padding-bottom: 16px;
    margin-bottom: 8px;
  }

  .links-nav ul {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 8px;
  }

  .links-nav button {
    border-left: none;
    border-bottom: 2px solid transparent;
    padding: 10px 12px;
    font-size: 0.8rem;
    background: var(--bg-secondary);
    border-radius: 6px;
    text-align: center;
  }

  .links-nav button.active {
    border-bottom-color: var(--accent);
    background: rgba(168, 85, 247, 0.1);
  }
}

@media (max-width: 480px) {
  .page-container {
    padding: 70px 12px 20px;
  }

  .links-nav ul {
    grid-template-columns: 1fr;
  }
}
</style>
