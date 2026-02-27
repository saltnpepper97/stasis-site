<script lang="ts">
  import { onMount } from 'svelte';
  import CodeBlock from '$lib/components/CodeBlock.svelte';

  type Section = { id: string; title: string };

  let activeSection = $state('');

  const sections: Section[] = [
    { id: 'app-detection', title: 'App Detection' },
    { id: 'regex-patterns', title: 'Regex Patterns' },
    { id: 'service-issues', title: 'Service Issues' },
    { id: 'config-reload', title: 'Config Reload' },
    { id: 'config-errors', title: 'Config Errors' },
    { id: 'help', title: 'More Help' }
  ];

  function getTopbarOffset() {
    // Mobile has no sticky topbar, but content has top padding.
    return window.innerWidth <= 968 ? 80 : 70;
  }

  function scrollToSection(id: string, behavior: ScrollBehavior) {
    const el = document.getElementById(id);
    if (!el) return;

    const offset = getTopbarOffset();
    const y = el.getBoundingClientRect().top + window.scrollY - offset;

    window.scrollTo({ top: y, behavior });
  }

  onMount(() => {
    const hash = window.location.hash.slice(1);
    if (hash) scrollToSection(hash, 'instant');

    const observer = new IntersectionObserver(
      (entries) => {
        for (const entry of entries) {
          if (entry.isIntersecting) {
            activeSection = (entry.target as HTMLElement).id;
          }
        }
      },
      { rootMargin: '-100px 0px -80% 0px' }
    );

    for (const { id } of sections) {
      const el = document.getElementById(id);
      if (el) observer.observe(el);
    }

    return () => observer.disconnect();
  });

  // Code examples
  const serviceLogsCode = `journalctl --user -u stasis.service`;
  const reloadCode = `systemctl --user restart stasis.service`;
  const videoGroupCode = `sudo gpasswd -a <user> video`;
</script>

<div class="page-container">
  <nav class="links-nav" aria-label="On this page">
    <div class="nav-title">On this page</div>
    <ul>
      {#each sections as section (section.id)}
        <li>
          <button
            type="button"
            class:active={activeSection === section.id}
            onclick={() => scrollToSection(section.id, 'smooth')}
          >
            {section.title}
          </button>
        </li>
      {/each}
    </ul>
  </nav>

  <main class="content">
    <h1>Frequently Asked Questions</h1>

    <section id="app-detection">
      <h2>Stasis Not Detecting Apps</h2>
      <p>If Stasis isn't detecting your applications for idle inhibition:</p>
      <ul>
        <li>Ensure your compositor is supported (see Supported Compositors)</li>
        <li>Check that the app names in <code>inhibit_apps</code> match the actual application names</li>
        <li>
          Use <code>stasis -v</code> or check <code>~/.cache/stasis/stasis.log</code> for detailed logs on detected apps
        </li>
      </ul>
    </section>

    <section id="regex-patterns">
      <h2>Regex Patterns Not Matching</h2>
      <p>If your regex patterns in <code>inhibit_apps</code> aren't working:</p>
      <ul>
        <li>Ensure you're using raw string syntax: <code>r"pattern"</code></li>
        <li>Test patterns with verbose logging to see what apps are detected</li>
        <li>Remember that River uses process-based detection (fallback) which may have different app names</li>
      </ul>
      <div class="info">
        <strong>💡 Tip:</strong>
        Run Stasis with verbose logging (<code>stasis -v</code>) to see exactly what application names are being
        detected, then adjust your patterns accordingly.
      </div>
    </section>

    <section id="service-issues">
      <h2>Service Not Starting</h2>
      <p>If your systemd service won't start:</p>
      <ul>
        <li>Verify the <code>ExecStart</code> path in your systemd service file points to the correct binary location</li>
        <li>Check service logs for specific errors:</li>
      </ul>
      <CodeBlock code={serviceLogsCode} language="bash" />
      <p>Common issues include incorrect binary paths or missing dependencies.</p>
    </section>

    <section id="config-reload">
      <h2>Configuration Not Reloading</h2>
      <p>If changes to your configuration aren't taking effect:</p>
      <ul>
        <li>Use <code>stasis reload</code> to send a reload signal to the running daemon</li>
        <li>Check configuration syntax if reload fails</li>
        <li>Restart the service if reload continues to fail:</li>
      </ul>
      <CodeBlock code={reloadCode} language="bash" />
    </section>

    <section id="config-errors">
      <h2>Configuration Errors</h2>
      <p>If Stasis reports configuration errors:</p>
      <ul>
        <li>Validate your RUNE syntax (see RUNE notes in documentation)</li>
        <li>
          Verify built-in action block names (fixed as of v0.1.2):
          <ul>
            <li><code>startup</code></li>
            <li><code>lock_screen</code> / <code>lock-screen</code></li>
            <li><code>dpms</code></li>
            <li><code>suspend</code></li>
            <li><code>brightness</code></li>
            <li><code>*</code> (custom actions can be named anything)</li>
          </ul>
        </li>
        <li>Check the manual: <code>man 5 stasis</code></li>
        <li>Use verbose logging to identify configuration issues</li>
      </ul>
    </section>

    <section id="help">
      <h2>Need More Help?</h2>
      <p>If your problem isn't listed here and you've tried everything:</p>
      <div class="info">
        <strong>🐛 Open a Bug Report</strong>
        <p>
          Visit the
          <a href="https://github.com/saltnpepper97/stasis/issues" target="_blank" rel="noopener noreferrer"
            >GitHub Issues</a
          >
          page to report your problem. Please include:
        </p>
        <ul>
          <li>Your distribution and compositor</li>
          <li>Stasis version (<code>stasis --version</code>)</li>
          <li>Relevant log output from <code>~/.cache/stasis/stasis.log</code></li>
          <li>Your configuration file (sanitize any sensitive info)</li>
        </ul>
      </div>
    </section>
  </main>
</div>

<style>
  .page-container {
    display: grid;
    grid-template-columns: 220px 1fr;
    gap: 40px;
    max-width: 1400px;
    margin: 0 auto;
    padding: 40px 20px;
  }

  .links-nav {
    position: sticky;
    top: 80px;
    height: fit-content;
    padding-top: 8px;
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
    padding: 6px 0 0 0;
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
    padding: 8px 0 8px 16px;
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
    scroll-margin-top: 70px;
  }

  section {
    margin-bottom: 48px;
    scroll-margin-top: 70px;
  }

  p,
  li {
    line-height: 1.7;
    color: var(--text-primary);
  }

  ul {
    line-height: 1.8;
    color: var(--text-primary);
    margin: 16px 0;
    padding-left: 24px;
  }

  li {
    margin: 8px 0;
  }

  a {
    color: var(--accent);
    text-decoration: none;
    transition: opacity 0.2s ease;
  }

  a:hover {
    opacity: 0.8;
    text-decoration: underline;
  }

  /* === BLOCKS === */
  .warning,
  .info {
    border-left: 4px solid;
    padding: 20px;
    margin: 24px 0;
    border-radius: 4px;
  }

  .warning {
    background: rgba(255, 193, 7, 0.1);
    border-left-color: #ffc107;
  }

  .info {
    background: var(--bg-secondary);
    border-left-color: var(--accent);
  }

  .warning strong,
  .info strong {
    display: block;
    margin-bottom: 12px;
    font-size: 1.05rem;
  }

  code {
    background: var(--bg-secondary);
    padding: 2px 6px;
    border-radius: 3px;
    font-family: 'Consolas', 'Monaco', 'Courier New', monospace;
    font-size: 0.9em;
    color: var(--text-primary);
    word-break: break-word;
  }

  /* === MOBILE: unify and simplify === */
  @media (max-width: 968px) {
    .page-container {
      grid-template-columns: 1fr;
      gap: 20px;
      padding: 80px 16px 20px;
    }

    .links-nav {
      position: static;
      border-bottom: 1px solid var(--border-color);
      padding-top: 12px;
      padding-bottom: 16px;
      margin-bottom: 8px;
    }

    .nav-title {
      font-size: 0.8rem;
      margin-bottom: 10px;
    }

    .links-nav ul {
      display: flex;
      flex-wrap: wrap;
      gap: 6px;
      padding: 0;
    }

    .links-nav button {
      border-left: none;
      border-bottom: 2px solid transparent;
      padding: 8px 12px;
      font-size: 0.85rem;
      background: var(--bg-secondary);
      border-radius: 6px;
      text-align: center;
    }

    .links-nav button.active {
      border-bottom-color: var(--accent);
      background: rgba(168, 85, 247, 0.1);
    }

    h1 {
      font-size: 2rem;
      margin-bottom: 24px;
    }

    h2 {
      font-size: 1.5rem;
      margin: 32px 0 12px 0;
      scroll-margin-top: 100px;
    }

    section {
      margin-bottom: 32px;
      scroll-margin-top: 100px;
    }

    p,
    ul {
      font-size: 0.95rem;
    }
  }

  /* === TINY DEVICES === */
  @media (max-width: 480px) {
    .links-nav ul {
      flex-direction: column;
    }

    .links-nav button {
      font-size: 0.75rem;
      padding: 8px 10px;
    }

    h1 {
      font-size: 1.75rem;
    }

    h2 {
      font-size: 1.25rem;
    }
  }
</style>
