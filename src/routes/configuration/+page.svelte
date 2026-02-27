<script lang="ts">
  import { onMount } from 'svelte';
  import CodeBlock from '$lib/components/CodeBlock.svelte';

  type Section = { id: string; title: string };

  let activeSection = $state('');

  const sections: Section[] = [
    { id: 'overview', title: 'Overview' },
    { id: 'globals', title: 'Globals (Variables)' },
    { id: 'default-block', title: 'Default Block' },
    { id: 'media', title: 'Media Monitoring' },
    { id: 'inhibitors', title: 'Inhibitors' },
    { id: 'notifications', title: 'Notifications' },
    { id: 'laptop', title: 'Laptop Settings' },
    { id: 'actions', title: 'Idle Actions' },
    { id: 'ac-battery', title: 'AC & Battery' },
    { id: 'example', title: 'Full Example' },
    { id: 'profiles', title: 'Profiles' }
  ];

  function topbarOffset() {
    return window.innerWidth <= 968 ? 80 : 70;
  }

  function scrollToSection(id: string, behavior: ScrollBehavior) {
    const el = document.getElementById(id);
    if (!el) return;

    const y = el.getBoundingClientRect().top + window.scrollY - topbarOffset();
    window.scrollTo({ top: y, behavior });
  }

  onMount(() => {
    const hash = window.location.hash.slice(1);
    if (hash) scrollToSection(hash, 'instant');

    const observer = new IntersectionObserver(
      (entries) => {
        for (const entry of entries) {
          if (entry.isIntersecting) activeSection = (entry.target as HTMLElement).id;
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

  // --- Code examples (RUNE) ---

  const globalsCode = `@author "Your Name"
@description "Stasis configuration"

# Top-level is intended for reusable values.
lock_after        300
screen_off_after  300
suspend_after     1800
debounce_seconds  4
notify_seconds    5`;

  const defaultBlockCode = `default:
  enable_loginctl false
  pre_suspend_command None
  monitor_media true
  ignore_remote_media true

  # Optional: ignore these media sources (case-insensitive)
  #media_blacklist ["spotify"]

  debounce_seconds debounce_seconds

  # Lid actions (laptop only — live here so they apply to both ac: and battery:)
  lid_close_action "hyprlock"
  #lid_open_action ""

  notify_on_unpause true

  # Global gate: per-step notifications only fire if this is true
  notify_before_action true

  inhibit_apps [
    r"steam_app_.*"
  ]

  startup:
    timeout 0
    command "notify-send 'Stasis idle manager started!'"
  end

  lock_screen:
    timeout lock_after
    command "hyprlock"
    resume_command "notify-send 'Welcome back $env.USER!'"
    notification "Locking soon"
    notify_seconds_before notify_seconds
  end

  dpms:
    timeout screen_off_after
    command "hyprctl dispatch dpms off"
    resume_command "hyprctl dispatch dpms on"
  end

  suspend:
    timeout suspend_after
    command "systemctl suspend"
  end
end`;

  const startupBlockCode = `startup:
  timeout 0
  command "notify-send 'Stasis idle manager started!'"
end`;

  const enableLoginctlCode = `# Enable global login1 tracking (lock/unlock state)
enable_loginctl true`;

  const preSuspendCode = `# Run before suspending (e.g. ensure screen is locked first)
pre_suspend_command "hyprlock"

# Disable:
pre_suspend_command None`;

  const profileIntroCode = `# Any top-level named block besides \`default:\` is a profile.
# Profiles override settings/steps from the active base and can
# have their own \`ac:\` / \`battery:\` plans.

gaming:
  mode "overlay"  # merges on top of active base

  # Replace the inherited inhibit_apps list:
  inhibit_apps [
    r".*\\.exe"
    r"steam_app_.*"
    r".*\\.x86_64"
  ]
end

work:
  mode "overlay"

  enable_loginctl true
  debounce_seconds 10
  monitor_media true
  ignore_remote_media true

  lock_screen:
    timeout 600
    command "hyprlock"
    resume_command "notify-send 'Welcome back, $env.USER!'"
  end

  dpms:
    timeout 300
    command "hyprctl dispatch dpms off"
    resume_command "hyprctl dispatch dpms on"
  end

  suspend:
    timeout 3600
    command "systemctl suspend"
  end
end

presentation:
  mode "fresh"  # starts from NOTHING — define every global and step you want

  pre_suspend_command None
  monitor_media false
  ignore_remote_media true
  debounce_seconds 0
  notify_on_unpause false
  notify_before_action false
  inhibit_apps [ ]

  # Clear lid actions so they don't fire during a presentation
  lid_close_action ""
  lid_open_action ""

  # Keep display on; no lock/dpms/suspend unless you add them
  brightness:
    timeout 0
    command "brightnessctl set 100%"
  end
end`;

  const profileSwitchCode = `# Switch profiles at runtime via IPC:
stasis profile gaming
stasis profile work
stasis profile presentation

# Return to default (no profile):
stasis profile default
# (alias)
stasis profile none`;

  const mediaMonitoringCode = `monitor_media true
ignore_remote_media true`;

  const mediaBlacklistCode = `media_blacklist ["spotify", "rhythmbox"]`;

  const appInhibitorsCode = `inhibit_apps [
  "vlc"
  "Spotify"
  "mpv"
  r".*\\.exe"
  r"steam_app_.*"
  r"firefox.*"
]`;

  const notifyOnUnpauseCode = `notify_on_unpause true`;

  const notifyBeforeActionCode = `notify_before_action true
notify_seconds_before 5`;

  const lidActionsCode = `# Lives under default: so it applies to both ac: and battery: plans.
# A profile can override or clear these.
lid_close_action "hyprlock"
lid_open_action  "brightnessctl set 60%"

# Clear an action entirely:
lid_open_action ""`;

  const debounceCode = `debounce_seconds 4`;

  const acPlanCode = `default:
  # ...globals, lid actions, desktop plan...

  ac:
    # timeout 0 runs immediately when AC becomes active
    custom_brightness_instant:
      timeout 0
      command "brightnessctl set 100%"
    end

    brightness:
      timeout 120
      command "brightnessctl set 30%"
    end

    dpms:
      timeout 60
      command "hyprctl dispatch dpms off"
      resume_command "hyprctl dispatch dpms on"
    end

    lock_screen:
      timeout 120
      command "hyprlock"
      notification "Locking on AC in 10s"
      notify_seconds_before 10
    end

    suspend:
      timeout 300
      command "systemctl suspend"
    end
  end
end`;

  const batteryPlanCode = `default:
  # ...globals, lid actions, desktop plan...

  battery:
    custom_brightness_instant:
      timeout 0
      command "brightnessctl set 40%"
    end

    brightness:
      timeout 60
      command "brightnessctl set 20%"
    end

    dpms:
      timeout 30
      command "hyprctl dispatch dpms off"
      resume_command "hyprctl dispatch dpms on"
    end

    lock_screen:
      timeout 120
      command "hyprlock"
      resume_command "notify-send 'Welcome back $env.USER!'"
    end

    suspend:
      timeout 120
      command "systemctl suspend"
    end
  end
end`;

  const fullExampleCode = `# This file was auto-generated by Stasis on first run
# Feel free to customize it to your needs
# Master config reference: /usr/share/doc/stasis/stasis.rune

@author "Dustin Pilgrim"
@description "Lightweight feature packed idle manager for Wayland"

# Top-level: reusable values (recommended)
lock_after        300
screen_off_after  300
suspend_after     1800
debounce_seconds  4
notify_seconds    5

default:
  enable_loginctl false
  pre_suspend_command None
  monitor_media true
  ignore_remote_media true  # ignore remote players (spotify/kdeconnect/etc.)

  # Optional: ignore these media sources for media inhibit (case-insensitive)
  #media_blacklist ["spotify"]

  debounce_seconds debounce_seconds

  # Lid actions (laptop only — live here so they apply to both ac: and battery:)
  lid_close_action "hyprlock"
  #lid_open_action ""

  # Notify when resuming from IPC pause (e.g. \`stasis pause 1h\`)
  notify_on_unpause true

  # Enables per-step notifications (only if the block sets \`notification\`)
  notify_before_action true

  inhibit_apps [
    "mpv"
    "vlc"
    "Spotify"
    r".*\\.exe"
    r"steam_app_.*"
    r"firefox.*"
  ]

  # ----------------------------------------------------------------
  # DESKTOP PLAN (used only on desktop chassis)
  # ----------------------------------------------------------------
  startup:
    timeout 0
    command "notify-send -a Stasis 'Stasis started!'"
  end

  early_dpms:
    timeout 300
    command "hyprctl dispatch dpms off"
    resume_command "hyprctl dispatch dpms on"
  end

  lock_screen:
    timeout lock_after
    command "hyprlock"
    resume_command "notify-send 'Welcome back $env.USER!'"
    notification "Locking session in 10s"
    notify_seconds_before 10
  end

  dpms:
    timeout screen_off_after
    command "hyprctl dispatch dpms off"
    resume_command "hyprctl dispatch dpms on"
  end

  suspend:
    timeout suspend_after
    command "systemctl suspend"
  end

  # ----------------------------------------------------------------
  # LAPTOP PLANS (used only on laptop chassis — ac: or battery:)
  # Desktop plan blocks above are ignored for laptops.
  # ----------------------------------------------------------------
  ac:
    # timeout 0 runs immediately when AC becomes active
    custom_brightness_instant:
      timeout 0
      command "brightnessctl set 100%"
    end

    brightness:
      timeout 120
      command "brightnessctl set 30%"
    end

    dpms:
      timeout 60
      command "hyprctl dispatch dpms off"
      resume_command "hyprctl dispatch dpms on"
    end

    lock_screen:
      timeout 120
      command "hyprlock"
      notification "Locking on AC in 10s"
      notify_seconds_before 10
    end

    suspend:
      timeout 300
      command "systemctl suspend"
    end
  end

  battery:
    custom_brightness_instant:
      timeout 0
      command "brightnessctl set 40%"
    end

    brightness:
      timeout 60
      command "brightnessctl set 20%"
    end

    dpms:
      timeout 30
      command "hyprctl dispatch dpms off"
      resume_command "hyprctl dispatch dpms on"
    end

    lock_screen:
      timeout 120
      command "hyprlock"
      resume_command "notify-send 'Welcome back $env.USER!'"
    end

    suspend:
      timeout 120
      command "systemctl suspend"
    end
  end
end

# --------------------------------------------------------------------
# PROFILES
#
# mode "overlay": merges on top of the active base (default/ac/battery)
# mode "fresh":   starts from nothing — define every global and step
# --------------------------------------------------------------------

# gaming: overlay — only replace inhibit_apps
gaming:
  mode "overlay"

  inhibit_apps [
    r".*\\.exe"
    r"steam_app_.*"
    r".*\\.x86_64"
  ]
end

# work: overlay — longer timeouts, loginctl enabled
work:
  mode "overlay"

  enable_loginctl true
  debounce_seconds 10
  monitor_media true
  ignore_remote_media true

  lock_screen:
    timeout 600
    command "hyprlock"
    resume_command "notify-send 'Welcome back, $env.USER!'"
  end

  dpms:
    timeout 300
    command "hyprctl dispatch dpms off"
    resume_command "hyprctl dispatch dpms on"
  end

  suspend:
    timeout 3600
    command "systemctl suspend"
  end
end

# presentation: fresh — keep display on, suppress all idle actions
presentation:
  mode "fresh"

  pre_suspend_command None
  monitor_media false
  ignore_remote_media true
  debounce_seconds 0
  notify_on_unpause false
  notify_before_action false
  inhibit_apps [ ]

  lid_close_action ""
  lid_open_action ""

  brightness:
    timeout 0
    command "brightnessctl set 100%"
  end
end

# Profile switching (runtime IPC):
#   stasis profile gaming
#   stasis profile work
#   stasis profile presentation
#   stasis profile default   (or: stasis profile none)`;
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
    <h1>Configuration</h1>

    <section id="overview">
      <h2>Overview</h2>
      <p>
        Stasis uses a <code>.rune</code> configuration file. It first looks for
        <code>$XDG_CONFIG_HOME/stasis/stasis.rune</code> (typically <code>~/.config/stasis/stasis.rune</code>).
        For system-wide configuration, copy the example to <code>/etc/stasis/stasis.rune</code>.
      </p>
      <p>
        On first run, Stasis generates a default configuration at <code>~/.config/stasis/stasis.rune</code>.
      </p>
      <p>
        The master reference config is located at <code>/usr/share/doc/stasis/stasis.rune</code>.
      </p>
    </section>

    <section id="globals">
      <h2>Globals (Variables)</h2>
      <p>
        The top level is intended for reusable values. Define timers and strings once, then reference them inside
        <code>default:</code> and profiles.
      </p>
      <CodeBlock code={globalsCode} language="rune" />
    </section>

    <section id="default-block">
      <h2>Default Block</h2>
      <p>
        Your config must contain a <code>default:</code> block. Most settings live under this block.
        Action blocks defined directly under <code>default:</code> form the desktop plan. On laptops,
        the <code>ac:</code> and <code>battery:</code> sub-blocks are used instead (see <a href="#ac-battery">AC &amp; Battery</a>).
      </p>
      <CodeBlock code={defaultBlockCode} language="rune" />

      <h3>Startup Action</h3>
      <p>The <code>startup:</code> block runs once when Stasis starts (always first):</p>
      <CodeBlock code={startupBlockCode} language="rune" />

      <h3>loginctl Integration</h3>
      <p>
        Set <code>enable_loginctl true</code> under <code>default:</code> to register Stasis with
        <code>login1</code> for global lock/unlock state tracking. This is a top-level global setting
        — there is no per-block <code>use_loginctl</code> option.
      </p>
      <CodeBlock code={enableLoginctlCode} language="rune" />

      <h3>Pre-Suspend Command</h3>
      <p>
        Optionally run a command immediately before suspending — useful to ensure the screen is locked
        before the system sleeps:
      </p>
      <CodeBlock code={preSuspendCode} language="rune" />
    </section>

    <section id="media">
      <h2>Media Monitoring</h2>

      <h3>Media Playback</h3>
      <p>Control whether Stasis monitors media playback to prevent idle actions:</p>
      <CodeBlock code={mediaMonitoringCode} language="rune" />
      <ul>
        <li><code>monitor_media</code> — active playback prevents idle timeouts</li>
        <li><code>ignore_remote_media</code> — ignores remote sources (Spotify remote, KDEConnect, etc.)</li>
      </ul>

      <h3>Media Blacklist</h3>
      <p>Ignore specific media sources (case-insensitive):</p>
      <CodeBlock code={mediaBlacklistCode} language="rune" />
    </section>

    <section id="inhibitors">
      <h2>Inhibitors</h2>

      <h3>Application Inhibitors</h3>
      <p>Specify apps/processes that prevent idle actions while running (strings or regex literals):</p>
      <CodeBlock code={appInhibitorsCode} language="rune" />

      <div class="info">
        <strong>Regex Pattern Guidelines:</strong>
        <ul>
          <li>Use raw string syntax: <code>r"pattern"</code></li>
          <li>Escape special characters: <code>\.</code> for literal dots</li>
          <li>Use <code>.*</code> for wildcards</li>
          <li>Use <code>^</code> and <code>$</code> for anchors</li>
        </ul>
      </div>
    </section>

    <section id="notifications">
      <h2>Notifications</h2>

      <h3>On Unpause</h3>
      <p>Notify when resuming from an IPC pause (e.g., <code>stasis pause 1h</code>):</p>
      <CodeBlock code={notifyOnUnpauseCode} language="rune" />

      <h3>Pre-Action</h3>
      <p>To show notifications before actions run:</p>
      <ul>
        <li>Enable the gate: <code>notify_before_action true</code></li>
        <li>Optionally set a default: <code>notify_seconds_before</code></li>
        <li>Set <code>notification "message"</code> on the action block</li>
        <li>Optionally override timing per-block with <code>notify_seconds_before</code></li>
      </ul>
      <CodeBlock code={notifyBeforeActionCode} language="rune" />
    </section>

    <section id="laptop">
      <h2>Laptop Settings</h2>

      <h3>Lid Actions</h3>
      <p>
        Configure what happens when the laptop lid closes or opens. These live directly under
        <code>default:</code> so they automatically apply to both <code>ac:</code> and
        <code>battery:</code> plans without duplication. Lid close/open also pause/resume the plan
        timers regardless. A profile can override or clear them.
      </p>
      <CodeBlock code={lidActionsCode} language="rune" />

      <h3>Debounce</h3>
      <p>Prevent rapid lid open/close events from triggering multiple actions:</p>
      <CodeBlock code={debounceCode} language="rune" />
    </section>

    <section id="actions">
      <h2>Idle Actions</h2>
      <p>Each action block supports:</p>
      <ul>
        <li><code>timeout</code> — seconds of idle time before triggering (required); <code>0</code> runs immediately on plan activation</li>
        <li><code>command</code> — command to run (required; use <code>None</code> to disable)</li>
        <li><code>resume_command</code> / <code>resume-command</code> — optional, run when activity resumes</li>
        <li><code>notification</code> — optional message shown before the action runs</li>
        <li><code>notify_seconds_before</code> — optional per-block timing override</li>
      </ul>

      <div class="info">
        <strong>Action names:</strong>
        <ul>
          <li><code>startup</code></li>
          <li><code>lock_screen</code> / <code>lock-screen</code></li>
          <li><code>early_dpms</code> — useful for a shorter initial screen-off before the main lock</li>
          <li><code>dpms</code></li>
          <li><code>brightness</code></li>
          <li><code>suspend</code></li>
          <li><code>*</code> — custom actions can be named anything</li>
        </ul>
      </div>
    </section>

    <section id="ac-battery">
      <h2>AC & Battery</h2>
      <p>
        Laptop plans live as sub-blocks <strong>inside</strong> <code>default:</code> (or inside a profile).
        When running on a laptop chassis, Stasis uses <code>ac:</code> or <code>battery:</code> depending
        on current power state — the desktop plan blocks are ignored. Globals and lid actions defined
        directly under <code>default:</code> still apply to both plans.
      </p>

      <h3>AC Plan</h3>
      <CodeBlock code={acPlanCode} language="rune" />

      <h3>Battery Plan</h3>
      <CodeBlock code={batteryPlanCode} language="rune" />

      <div class="info">
        <strong>💡 Tips:</strong>
        <ul>
          <li>Put <code>timeout 0</code> actions first — they run immediately when the plan activates (e.g., restore brightness on plug-in).</li>
          <li>Profiles can also define their own <code>ac:</code> / <code>battery:</code> sub-blocks to override the laptop plan per-profile.</li>
        </ul>
      </div>
    </section>

    <section id="example">
      <h2>Full Example</h2>
      <p>
        Complete example covering desktop plan, laptop AC/battery plans, and all three profile modes
        (<code>overlay</code> and <code>fresh</code>):
      </p>
      <CodeBlock code={fullExampleCode} language="rune" />
    </section>

    <div class="bonus-divider">
      <span>Advanced</span>
    </div>

    <section id="profiles" class="bonus-section">
      <h2>Profiles</h2>
      <p>
        Profiles are any top-level named blocks other than <code>default</code>. They override settings
        and action blocks from the active base plan, and can define their own <code>ac:</code>/<code>battery:</code> plans.
      </p>

      <div class="info">
        <strong>mode:</strong>
        <ul>
          <li><code>"overlay"</code> (default) — merges on top of the active base; only what you specify is overridden</li>
          <li><code>"fresh"</code> — starts from nothing; you must define every global and action block you want</li>
        </ul>
      </div>

      <CodeBlock code={profileIntroCode} language="rune" />

      <div class="info">
        <strong>Profile selection:</strong>
        <p>Profiles are switched at runtime via IPC:</p>
        <CodeBlock code={profileSwitchCode} language="bash" />
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
    align-self: start;
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
    scroll-margin-top: 70px;
  }

  h3 {
    font-size: 1.3rem;
    font-weight: 600;
    margin: 32px 0 12px 0;
    color: var(--text-primary);
  }

  section {
    margin-bottom: 48px;
    scroll-margin-top: 70px;
  }

  p {
    line-height: 1.7;
    color: var(--text-primary);
    margin: 16px 0;
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

  .warning {
    background: rgba(255, 193, 7, 0.1);
    border-left: 4px solid #ffc107;
    padding: 20px;
    margin: 24px 0;
    border-radius: 4px;
  }

  .warning strong {
    display: block;
    margin-bottom: 12px;
    font-size: 1.05rem;
  }

  .warning p {
    margin: 8px 0;
  }

  .info {
    background: var(--bg-secondary);
    border-left: 4px solid var(--accent);
    padding: 20px;
    margin: 24px 0;
    border-radius: 4px;
  }

  .info strong {
    display: block;
    margin-bottom: 8px;
    color: var(--accent);
  }

  .info ul {
    margin: 8px 0;
  }

  .bonus-divider {
    display: flex;
    align-items: center;
    gap: 16px;
    margin: 64px 0 0 0;
    color: var(--text-secondary);
    font-size: 0.8rem;
    font-weight: 600;
    text-transform: uppercase;
    letter-spacing: 1px;
  }

  .bonus-divider::before,
  .bonus-divider::after {
    content: '';
    flex: 1;
    height: 1px;
    background: var(--border-color);
  }

  .bonus-section {
    margin-top: 16px;
  }

  code {
    background: var(--bg-secondary);
    padding: 2px 6px;
    border-radius: 3px;
    font-family: 'Consolas', 'Monaco', 'Courier New', monospace;
    font-size: 0.9em;
    color: var(--text-primary);
  }

  a {
    color: var(--accent);
    text-decoration: none;
  }

  a:hover {
    text-decoration: underline;
  }

  @media (max-width: 968px) {
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
      padding: 10px 8px;
      font-size: 0.75rem;
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

    p,
    ul {
      font-size: 0.95rem;
    }

    ul {
      padding-left: 20px;
    }

    .warning,
    .info {
      padding: 14px;
      font-size: 0.9rem;
    }

    .warning strong,
    .info strong {
      font-size: 0.95rem;
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
