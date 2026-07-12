<script lang="ts">
  import { asset, resolve } from '$app/paths';
</script>

<main>
  <section class="hero">
    <div class="hero-copy">
      <p class="kicker">Wayland idle management</p>
      <h1>Time to step back.</h1>
      <p class="lede">
        Stasis is a context-aware idle manager for Wayland. It runs an ordered plan when you are away,
        then gets out of the way when your session is active.
      </p>
      <div class="actions">
        <a class="primary" href={resolve('/quick-start')}>Get started <span>→</span></a>
        <a class="secondary" href={resolve('/configuration')}>Read the configuration</a>
      </div>
    </div>

    <div class="hero-mark" aria-hidden="true">
      <div class="mark-frame">
        <img src={asset('/favicon.png')} alt="" />
      </div>
      <p>Idle, without the surprises.</p>
    </div>
  </section>

  <section class="release-brief" aria-labelledby="release-heading">
    <div class="brief-heading">
      <p class="kicker">Current release</p>
      <h2 id="release-heading">1.4.0</h2>
      <p>A tighter relationship with your shell, hardware, and session state.</p>
    </div>
    <div class="brief-list">
      <article>
        <span>01</span>
        <h3>Live events</h3>
        <p><code>stasis watch</code> streams state, pause, and profile changes to custom shell integrations.</p>
      </article>
      <article>
        <span>02</span>
        <h3>Lower-power idle</h3>
        <p>Optional GPU power saving follows DPMS and restores the original settings when you return.</p>
      </article>
      <article>
        <span>03</span>
        <h3>Lock-aware sleep</h3>
        <p>LockedHint tracking and <code>prepare_sleep_command</code> improve awareness around locking and suspend.</p>
      </article>
    </div>
  </section>

  <section class="capabilities" aria-labelledby="capabilities-heading">
    <div>
      <p class="kicker">What it handles</p>
      <h2 id="capabilities-heading">A small system with clear boundaries.</h2>
    </div>
    <ol>
      <li><strong>01 / Ordered actions</strong><span>Build a plan that dims, locks, powers down displays, and suspends on your timing.</span></li>
      <li><strong>02 / Real activity</strong><span>Media, applications, portals, and compositor activity can keep the plan from firing at the wrong time.</span></li>
      <li><strong>03 / Profiles</strong><span>Switch between focused, gaming, presentation, or laptop setups without rewriting your base config.</span></li>
      <li><strong>04 / Observable state</strong><span>Use the tray, status JSON, or the event stream to make Stasis visible in the rest of your desktop.</span></li>
    </ol>
  </section>
</main>

<style>
  main { max-width: var(--content-width); margin: 0 auto; padding: 34px 24px 0; }
  .hero { display: grid; grid-template-columns: minmax(0, 1.25fr) minmax(280px, .75fr); gap: clamp(48px, 10vw, 150px); align-items: center; min-height: min(700px, calc(100vh - 72px)); padding: 80px 0; border-bottom: 1px solid var(--rule); }
  .kicker { margin: 0 0 14px; color: var(--signal); font-family: var(--font-mono); font-size: .7rem; font-weight: 600; letter-spacing: .12em; text-transform: uppercase; }
  h1, h2 { margin: 0; color: var(--ink); font-family: var(--font-display); font-weight: 600; letter-spacing: -.07em; line-height: .9; }
  h1 { max-width: 700px; font-size: clamp(4.3rem, 10vw, 9.5rem); }
  .lede { max-width: 590px; margin: 32px 0 0; color: var(--muted); font-size: clamp(1.05rem, 1.5vw, 1.28rem); line-height: 1.65; }
  .actions { display: flex; flex-wrap: wrap; gap: 12px; margin-top: 36px; }
  .actions a { display: inline-flex; align-items: center; gap: 22px; padding: 13px 18px; border: 1px solid var(--ink); text-decoration: none; font-size: .9rem; font-weight: 700; }
  .primary { background: var(--ink); color: var(--surface); }
  .primary:hover { background: var(--signal); border-color: var(--signal); }
  .secondary { color: var(--ink); }
  .secondary:hover { background: var(--signal-soft); border-color: var(--signal); }
  .hero-mark { display: grid; justify-items: center; gap: 20px; }
  .mark-frame { display: grid; place-items: center; width: min(100%, 310px); aspect-ratio: 1; border: 1px solid var(--ink); background: var(--surface); box-shadow: 18px 18px 0 var(--signal); }
  .mark-frame img { width: 68%; height: 68%; object-fit: contain; }
  .hero-mark p { margin: 0; color: var(--muted); font-family: var(--font-mono); font-size: .72rem; letter-spacing: .08em; text-transform: uppercase; }
  .release-brief { display: grid; grid-template-columns: 1fr 2fr; gap: clamp(32px, 8vw, 120px); padding: 88px 0; border-bottom: 1px solid var(--rule); }
  .brief-heading h2 { font-size: clamp(3.5rem, 6vw, 6rem); }
  .brief-heading > p:last-child { max-width: 300px; color: var(--muted); }
  .brief-list { display: grid; grid-template-columns: repeat(3, 1fr); border-top: 1px solid var(--rule); }
  .brief-list article { min-height: 240px; padding: 20px 18px 10px; border-right: 1px solid var(--rule); }
  .brief-list article:first-child { border-left: 1px solid var(--rule); }
  .brief-list span { color: var(--signal); font-family: var(--font-mono); font-size: .72rem; }
  .brief-list h3 { margin: 52px 0 10px; font-size: 1rem; }
  .brief-list p { color: var(--muted); font-size: .92rem; line-height: 1.6; }
  .capabilities { display: grid; grid-template-columns: .8fr 1.2fr; gap: clamp(36px, 9vw, 130px); padding: 104px 0 18px; }
  .capabilities h2 { font-size: clamp(2.8rem, 5vw, 5rem); }
  .capabilities ol { margin: 0; padding: 0; border-top: 1px solid var(--rule); list-style: none; }
  .capabilities li { display: grid; grid-template-columns: 190px 1fr; gap: 24px; padding: 19px 0; border-bottom: 1px solid var(--rule); }
  .capabilities strong { font-family: var(--font-mono); font-size: .73rem; letter-spacing: .05em; }
  .capabilities span { color: var(--muted); }
  @media (max-width: 800px) { main { padding-inline: 18px; } .hero, .release-brief, .capabilities { grid-template-columns: 1fr; } .hero { min-height: auto; padding: 92px 0 68px; } .hero-mark { max-width: 260px; margin: 12px auto 0; } .brief-list { grid-template-columns: 1fr; } .brief-list article { min-height: auto; border-left: 1px solid var(--rule); border-bottom: 1px solid var(--rule); } .brief-list h3 { margin-top: 28px; } .capabilities { padding-top: 76px; } }
  @media (max-width: 480px) { h1 { font-size: 4rem; } .capabilities li { grid-template-columns: 1fr; gap: 8px; } }
</style>
