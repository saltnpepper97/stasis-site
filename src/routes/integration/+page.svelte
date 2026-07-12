<script lang="ts">
  import { onMount } from 'svelte';
  import CodeBlock from '$lib/components/CodeBlock.svelte';
  import PageNav from '$lib/components/PageNav.svelte';
  
  let activeSection = $state('');
  
  const sections = [
    { id: 'watch', title: 'Live Events' },
    { id: 'status-json', title: 'Status JSON' },
    { id: 'waybar', title: 'Waybar' },
    { id: 'tray', title: 'System Tray' }
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
    "idle_waiting": "",
    "idle_inhibited": "",
    "manually_inhibited": "",
    "locked": "",
    "not_running": "󰒲"
  },
  "tooltip": true,
  "on-click": "stasis toggle-inhibit",
  "interval": 2,
  "restart-interval": 2,
  "return-type": "json"
}`;

  const statusJsonCode = `{
  "text": "active",
  "alt": "idle_active",
  "class": "idle_active",
  "tooltip": "Profile: default\\nPlan Source: Desktop\\nState: active",
  "profile": "default"
}`;

  const watchCode = `stasis watch
# {"state":"waiting","paused":false,"manually_paused":false,"profile":"default"}
# {"state":"manual","paused":true,"manually_paused":true,"profile":"work"}`;

  const waybarTextCode = `"custom/stasis": {
  "exec": "stasis info --json",
  "format": "{text}",
  "tooltip": true,
  "on-click": "stasis toggle-inhibit",
  "interval": 2,
  "restart-interval": 2,
  "return-type": "json"
}`;

  const trayServiceCode = `[Unit]
Description=Stasis System Tray Frontend
PartOf=graphical-session.target
After=graphical-session.target stasis.service
ConditionPathExists=%t

[Service]
Type=simple
ExecStart=/usr/bin/stasis tray
Restart=on-failure
RestartSec=2

[Install]
WantedBy=graphical-session.target`;
</script>

<div class="page-container docs-page">
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

    <section id="watch">
      <h2>Live Events</h2>

      <p>
        Stasis 1.4.0 adds <code>stasis watch</code> for custom shells and other integrations. It prints the current
        state immediately, then writes another JSON object only when the state, pause status, or active
        profile changes. Any program that can keep a process open and read lines can use it instead of polling
        <code>stasis info</code>.
      </p>
      <CodeBlock code={watchCode} language="bash" />
      <p>
        Each line includes <code>state</code>, <code>paused</code>, <code>manually_paused</code>, and
        <code>profile</code>. The state is one of <code>waiting</code>, <code>active</code>,
        <code>inhibited</code>, <code>locked</code>, or <code>manual</code>.
      </p>
    </section>

    <section id="status-json">
      <h2>Status JSON</h2>
      <p>
        <code>stasis info --json</code> is the stable status-bar contract. The <code>text</code> field uses short labels:
        <code>waiting</code>, <code>active</code>, <code>inhibited</code>, <code>locked</code>, and <code>manual</code>.
      </p>
      <p>
        The <code>alt</code> and <code>class</code> fields are intended for icon mapping and CSS styling. Tooltips include
        the active profile, plan source, state, manual pause status, app/media inhibitor counts, D-Bus inhibit state,
        and the next pending action when available.
      </p>
      <CodeBlock code={statusJsonCode} language="json" />
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

    <section id="tray">
      <h2>System Tray</h2>

      <p>
        <code>stasis tray</code> runs the optional StatusNotifier tray frontend. It is separate from
        the headless daemon and does not replace <code>stasis info --json</code>, so Waybar and other
        status bars can keep polling the JSON output directly.
      </p>

      <p>
        The tray tooltip shows the current Stasis state. Its menu provides actions to toggle
        manual inhibition, pause, resume, reload the config, and quit only the tray process.
      </p>

      <div class="info-card">
        <strong>Tray host required</strong>
        <p>
          Start a StatusNotifier tray host first, such as Waybar's tray module, KDE Plasma,
          or another panel with tray support. The daemon remains headless and does not launch
          the tray automatically.
        </p>
      </div>

      <h3>Run Manually</h3>
      <CodeBlock code="stasis tray" language="bash" />

      <h3>Systemd User Service</h3>
      <p>
        Run the daemon and tray as separate user services. Packages may already install this
        optional service file as <code>stasis-tray.service</code>.
      </p>
      <CodeBlock code={trayServiceCode} language="ini" />

      <p>
        NixOS and Home Manager users can enable the tray through the module option
        <code>services.stasis.tray.enable</code>.
      </p>
    </section>
    <PageNav />
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

.info-card {
  margin: 20px 0;
  padding: 18px 20px;
  border: 1px solid var(--border-color);
  border-radius: var(--radius-card);
  background:
    linear-gradient(180deg, var(--surface-highlight), transparent),
    var(--surface-glass);
  box-shadow: var(--shadow-glow);
}

.info-card strong {
  display: block;
  color: var(--accent);
  margin-bottom: 8px;
}

.info-card p {
  margin: 0;
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
    background: var(--surface-glass);
    border-radius: 999px;
    text-align: center;
  }

  .links-nav button.active {
    border-bottom-color: var(--accent);
    background: color-mix(in srgb, var(--accent) 12%, transparent);
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
