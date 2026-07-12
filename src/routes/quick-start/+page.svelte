<script lang="ts">
  import { onMount } from 'svelte';
  import CodeBlock from '$lib/components/CodeBlock.svelte';
  import PageNav from '$lib/components/PageNav.svelte';
  
  let activeSection = $state('');
  
  const sections = [
    { id: 'install', title: 'Installation' },
    { id: 'session', title: 'Session Setup' },
    { id: 'lockers', title: 'Screen Lockers' },
    { id: 'manual', title: 'Running Manually' },
    { id: 'systemd', title: 'Systemd Service' },
  ];
  
  onMount(() => {
    // Handle anchor links from other pages
    const hash = window.location.hash.slice(1);
    if (hash) {
      const element = document.getElementById(hash);
      if (element) {
        const topbarHeight = window.innerWidth <= 768 ? 0 : 70;
        const elementPosition = element.getBoundingClientRect().top + window.scrollY;
        const offsetPosition = elementPosition - topbarHeight;
        
        window.scrollTo({
          top: offsetPosition,
          behavior: 'instant'
        });
      }
    }
    
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
    if (element) {
      const topbarHeight = window.innerWidth <= 768 ? 80 : 70;
      const elementPosition = element.getBoundingClientRect().top + window.scrollY;
      const offsetPosition = elementPosition - topbarHeight;
      
      window.scrollTo({
        top: offsetPosition,
        behavior: 'smooth'
      });
    }
  }
  
  const systemdServiceCode = `[Unit]
Description=Stasis Wayland Idle Manager
PartOf=graphical-session.target
After=graphical-session.target
ConditionPathExists=%t

[Service]
Type=simple
ExecStart=/usr/bin/stasis
ExecReload=/usr/bin/stasis reload
Restart=on-failure
RestartSec=2

[Install]
WantedBy=graphical-session.target`; 

  const archInstallCode = `yay -S stasis
# or track git builds
yay -S stasis-git`;

  const nixInstallCode = `nix build 'github:saltnpepper97/stasis#stasis'`;

  const sourceInstallCode = `git clone https://github.com/saltnpepper97/stasis
cd stasis
cargo build --release --locked
sudo install -Dm755 target/release/stasis /usr/local/bin/stasis
sudo install -Dm644 assets/stasis.png /usr/local/share/icons/hicolor/256x256/apps/stasis.png`;

  const lockerWrapperCode = `#!/usr/bin/env bash
# Tell logind we are locking; Stasis listens for this when enable_loginctl_integration is true.
loginctl lock-session

# Run your locker in the background if it must daemonize or fork.
swaylock -f`;

  const lockerConfigCode = `default:
  enable_loginctl_integration true

  lock_screen:
    timeout 300
    command "~/.local/bin/stasis-lock.sh"
  end
end`;
  
  const enableServiceCode = `# Reload systemd to recognize the new service
systemctl --user daemon-reload

# Enable and start the service
systemctl --user enable --now stasis.service`;
</script>

<div class="page-container docs-page">
  <nav class="links-nav">
    <div class="nav-title">On this page</div>
    <ul>
      {#each sections as section}
        <li>
          <button
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
    <h1>Quick Start</h1>

    <section id="install">
      <h2>Installation</h2>
      <p>Install Stasis from your package manager when available, or build it from source.</p>

      <h3>Arch Linux</h3>
      <CodeBlock code={archInstallCode} language="bash" />

      <h3>Nix / NixOS</h3>
      <CodeBlock code={nixInstallCode} language="bash" />

      <h3>From Source</h3>
      <p>
        Runtime dependencies include Wayland, D-Bus, and <code>pulseaudio</code> or
        <code>pipewire-pulse</code> for media detection through <code>pactl</code>.
      </p>
      <CodeBlock code={sourceInstallCode} language="bash" />
    </section>

    <section id="session">
      <h2>Session Setup</h2>
      <p>
        Start your compositor inside a real D-Bus session if you use
        <code>enable_dbus_inhibit</code>, loginctl lock tracking, lid events, or suspend/resume integration.
      </p>

      <div class="note">
        Use your compositor or distribution's recommended launcher, such as <code>niri-session</code>,
        <code>dbus-run-session</code>, or another session wrapper that provides
        <code>DBUS_SESSION_BUS_ADDRESS</code> to user services.
      </div>
    </section>

    <section id="lockers">
      <h2>Screen Lockers</h2>
      <p>
        Stasis normally tracks a lock step by waiting for the configured locker process to exit.
        If your locker daemonizes or forks immediately, Stasis can interpret that as an immediate unlock.
      </p>

      <div class="warning">
        <strong>Do not daemonize unless you also enable loginctl tracking.</strong>
        Remove options like <code>swaylock -f</code> when possible. If your setup requires a backgrounding locker,
        use <code>enable_loginctl_integration true</code> and a wrapper script.
      </div>

      <h3>Wrapper Script Pattern</h3>
      <CodeBlock code={lockerWrapperCode} language="bash" />
      <p>Save this as <code>~/.local/bin/stasis-lock.sh</code>, make it executable, then reference it from your config:</p>
      <CodeBlock code={lockerConfigCode} language="rune" />
    </section>
 
    <section id="manual">
      <h2>Running Manually</h2>
      <p>
        For testing purposes, you can run Stasis directly from the command line. 
        Make sure you're in a running Wayland session, then simply run:
      </p>
      <CodeBlock code="stasis" />
      
      <p>
        This is useful for testing your configuration, but for daily use we strongly 
        recommend setting up the systemd service below for automatic startup.
      </p>
    </section>
    
    <section id="systemd">
      <h2>Systemd Service (Recommended)</h2>
      <p>
        The recommended way to run Stasis is as a systemd user service. This ensures 
        Stasis starts automatically with your graphical session and restarts if it crashes.
      </p>

      <h3>Provided Service File</h3>
      <p>
        Stasis provides a user service file when installed through packages such as the AUR.
        Enable it once, then start it from your compositor's autostart if your session target does not start it automatically:
      </p>

      <CodeBlock code="systemctl --user enable stasis.service" />

      <p>
        Then you can start Stasis via your compositors autostart section using the 
        following:
      </p>

      <CodeBlock code="systemctl --user start stasis" />
      
      <h3>Create the Service File</h3>
      <p>
        If you installed Stasis manually and want to create a user only service file in your home directory,
        Create a service file at <code>~/.config/systemd/user/stasis.service</code> with this content:
      </p>
      
      <CodeBlock code={systemdServiceCode} language="ini" />
      
      <div class="note">
        <strong>Path Note:</strong> The service file above assumes Stasis is installed in <code>/usr/bin/stasis</code>.
        If you installed Stasis to a different location (e.g., <code>~/.cargo/bin/stasis</code>), 
        update the <code>ExecStart=</code> line accordingly.
      </div>
      
      <h3>Enable and Start</h3>
      <p>Enable and start the service with these commands:</p>
      <CodeBlock code={enableServiceCode} language="bash" />
      
      <p>
        Now start Stasis from your compositors autostart section 
        e.g. for Hyprland:
      </p>
      <CodeBlock code="exec-once = systemctl --user start stasis" />
    </section>
    <PageNav/>
  </main>
</div>

<style>
  .page-container {
    display: grid;
    grid-template-columns: 200px 1fr;
    gap: 40px;
    max-width: 1200px;
    margin: 0 auto;
    padding: 40px 20px;
  }
  
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
    font-size: 1.3rem;
    font-weight: 600;
    margin: 32px 0 12px 0;
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
  
  ul {
    line-height: 1.7;
    color: var(--text-primary);
    margin: 16px 0;
    padding-left: 24px;
  }
  
  li {
    margin: 8px 0;
  }
  
  .warning {
    background: color-mix(in srgb, var(--accent-strong) 9%, var(--surface-glass));
    border-left: 4px solid var(--accent-strong);
    padding: 16px;
    margin: 24px 0;
    border-radius: 14px;
  }
  
  .note {
    background: color-mix(in srgb, var(--accent) 8%, var(--surface-glass));
    border-left: 4px solid var(--accent);
    padding: 16px;
    margin: 24px 0;
    border-radius: 14px;
  }
  
  code {
    background: var(--surface-glass);
    padding: 2px 6px;
    border-radius: 6px;
    font-family: 'Consolas', 'Monaco', 'Courier New', monospace;
    font-size: 0.9em;
    color: var(--text-primary);
  }
  
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
    
    .nav-title {
      font-size: 0.8rem;
      margin-bottom: 10px;
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
      border-left: none;
      background: color-mix(in srgb, var(--accent) 12%, transparent);
    }
    
    h1 {
      font-size: 2rem;
      margin-bottom: 24px;
    }
    
    h2 {
      font-size: 1.4rem;
      margin: 32px 0 12px 0;
      scroll-margin-top: 100px;
    }
    
    h3 {
      font-size: 1.15rem;
      margin: 24px 0 10px 0;
    }
    
    section {
      margin-bottom: 32px;
      scroll-margin-top: 100px;
    }
    
    p {
      font-size: 0.95rem;
    }
    
    .warning,
    .note {
      padding: 12px;
      font-size: 0.9rem;
    }
    
    code {
      font-size: 0.85em;
    }
  }
  
  @media (max-width: 480px) {
    .page-container {
      padding: 70px 12px 20px;
    }
    
    .links-nav ul {
      grid-template-columns: 1fr;
    }
    
    .links-nav button {
      padding: 8px 10px;
      font-size: 0.75rem;
    }
    
    h1 {
      font-size: 1.75rem;
    }
    
    h2 {
      font-size: 1.25rem;
    }
    
    h3 {
      font-size: 1.1rem;
    }
  }
</style>
