<script lang="ts">
  import { base } from '$app/paths';
  import stasisLogo from '$lib/assets/favicon.png';
</script>

<div class="hero">
  <div class="hero-content">
    <img src={stasisLogo} alt="Stasis Logo" class="logo" />
    <h1>Stasis</h1>
    <p class="tagline">A modern Wayland idle manager that knows when to step back.</p>
    <p class="subtitle">Keep your session in perfect balance—automatically preventing idle when it matters, allowing it when it doesn't.</p>

    <div class="cta-buttons">
      <a href="{base}/quick-start" class="btn btn-primary">Get Started</a>
      <a
        href="https://github.com/saltnpepper97/stasis"
        class="btn btn-secondary"
        target="_blank"
        rel="noopener noreferrer"
      >
        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="currentColor">
          <path d="M12 0c-6.626 0-12 5.373-12 12 0 5.302 3.438 9.8 8.207 11.387.599.111.793-.261.793-.577v-2.234c-3.338.726-4.033-1.416-4.033-1.416-.546-1.387-1.333-1.756-1.333-1.756-1.089-.745.083-.729.083-.729 1.205.084 1.839 1.237 1.839 1.237 1.07 1.834 2.807 1.304 3.492.997.107-.775.418-1.305.762-1.604-2.665-.305-5.467-1.334-5.467-5.931 0-1.311.469-2.381 1.236-3.221-.124-.303-.535-1.524.117-3.176 0 0 1.008-.322 3.301 1.23.957-.266 1.983-.399 3.003-.404 1.02.005 2.047.138 3.006.404 2.291-1.552 3.297-1.23 3.297-1.23.653 1.653.242 2.874.118 3.176.77.84 1.235 1.911 1.235 3.221 0 4.609-2.807 5.624-5.479 5.921.43.372.823 1.102.823 2.222v3.293c0 .319.192.694.801.576 4.765-1.589 8.199-6.086 8.199-11.386 0-6.627-5.373-12-12-12z"/>
        </svg>
        View on GitHub
      </a>
    </div>
  </div>
</div>

<section class="features">
  <div class="feature-grid">
    <div class="feature-card">
      <div class="feature-icon">🧠</div>
      <h3>Context-Aware</h3>
      <p>Watching a video? Reading a document? Stasis understands your activity and prevents unwanted screen locks automatically.</p>
    </div>

    <div class="feature-card">
      <div class="feature-icon">🎵</div>
      <h3>Media Detection</h3>
      <p>Automatically detects media playback and honors Wayland idle inhibitor protocols—no more mouse jiggles during movies.</p>
    </div>

    <div class="feature-card">
      <div class="feature-icon">⚙️</div>
      <h3>Flexible Actions</h3>
      <p>Configure custom actions with regex patterns, application-specific rules, and named action blocks for complete control.</p>
    </div>

    <div class="feature-card">
      <div class="feature-icon">⚡</div>
      <h3>Live Reload</h3>
      <p>Update your configuration on the fly without restarting. Changes take effect immediately.</p>
    </div>

    <div class="feature-card">
      <div class="feature-icon">🛌</div>
      <h3>Lid Event Handling</h3>
      <p>Detects laptop lid open/close events via DBus to intelligently manage idle behavior.</p>
    </div>

    <div class="feature-card">
      <div class="feature-icon">📝</div>
      <h3>Clean Config</h3>
      <p>Uses the intuitive RUNE configuration language for easy setup and maintenance.</p>
    </div>
  </div>
</section>

<style>
  /* Brand helpers (match the new logo: cosmic violet + burnt core) */
  :global([data-theme="dark"]) {
    --brand-violet: #4B2E83;
    --brand-deep: #1C1233;
    --brand-ember: #C7782B;   /* burnt orange core */
    --brand-ember-deep: #A45A1F;
    --brand-lavender: #C8B6FF;
  }

  :global([data-theme="light"]) {
    --brand-violet: #4B2E83;
    --brand-deep: #1C1233;
    --brand-ember: #C7782B;
    --brand-ember-deep: #A45A1F;
    --brand-lavender: #C8B6FF;
  }

  .hero {
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;

    /* softer, calmer background wash using new brand colors */
    background:
      radial-gradient(900px 600px at 50% 35%,
        rgba(199, 120, 43, 0.10),
        transparent 60%),
      radial-gradient(900px 700px at 30% 70%,
        rgba(75, 46, 131, 0.10),
        transparent 65%),
      linear-gradient(180deg,
        rgba(75, 46, 131, 0.05),
        rgba(199, 120, 43, 0.03));

    padding: 40px 20px;
    width: 100%;
    margin-left: 50%;
    transform: translateX(-50%);
  }

  .hero-content {
    max-width: 800px;
    text-align: center;
  }

  .logo {
    width: 180px;
    height: 180px;
    margin: 0 auto 32px;
    display: block;

    /* calm suspended glow, not neon */
    filter:
      drop-shadow(0 10px 26px rgba(75, 46, 131, 0.22))
      drop-shadow(0 6px 18px rgba(199, 120, 43, 0.16));
    animation: float 3s ease-in-out infinite;
  }

  @keyframes float {
    0%, 100% { transform: translateY(0px); }
    50% { transform: translateY(-10px); }
  }

  h1 {
    font-family: 'Space Grotesk', sans-serif;
    font-size: 4.5rem;
    font-weight: 700;
    margin: 0 0 24px 0;

    /* logo-matching title gradient */
    background: linear-gradient(135deg, var(--brand-violet) 0%, var(--brand-ember) 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;

    letter-spacing: -2px;
  }

  .tagline {
    font-family: 'Space Grotesk', sans-serif;
    font-size: 1.5rem;
    font-weight: 500;
    color: var(--text-primary);
    margin: 0 0 16px 0;
    line-height: 1.4;
  }

  .subtitle {
    font-size: 1.1rem;
    color: var(--text-secondary);
    margin: 0 0 40px 0;
    line-height: 1.6;
    max-width: 600px;
    margin-left: auto;
    margin-right: auto;
  }

  .cta-buttons {
    display: flex;
    gap: 16px;
    justify-content: center;
    margin-bottom: 32px;
  }

  .btn {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    padding: 14px 32px;
    border-radius: 10px;
    font-family: 'Space Grotesk', sans-serif;
    font-weight: 600;
    font-size: 1.05rem;
    text-decoration: none;
    transition: transform 0.2s ease, box-shadow 0.2s ease, border-color 0.2s ease, background 0.2s ease;
    border: none;
    cursor: pointer;
  }

  .btn-primary {
    background: linear-gradient(135deg, var(--brand-violet) 0%, var(--brand-ember) 100%);
    color: #fff;
    box-shadow:
      0 10px 26px rgba(75, 46, 131, 0.22),
      0 6px 18px rgba(199, 120, 43, 0.16);
  }

  .btn-primary:hover {
    transform: translateY(-2px);
    box-shadow:
      0 14px 34px rgba(75, 46, 131, 0.26),
      0 10px 24px rgba(199, 120, 43, 0.20);
  }

  /* Secondary button stays calm and “system UI” */
  .btn-secondary {
    background: var(--bg-secondary);
    color: var(--text-primary);
    border: 2px solid var(--border-color);
  }

  .btn-secondary:hover {
    border-color: var(--brand-lavender);
    transform: translateY(-2px);
  }

  .features {
    padding: 80px 20px;
    max-width: 1200px;
    margin: 0 auto;
  }

  .feature-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
    gap: 32px;
  }

  .feature-card {
    background: var(--bg-secondary);
    border: 1px solid var(--border-color);
    border-radius: 12px;
    padding: 32px;
    transition: transform 0.3s ease, border-color 0.3s ease, box-shadow 0.3s ease;
  }

  .feature-card:hover {
    transform: translateY(-4px);
    border-color: rgba(200, 182, 255, 0.35);
    box-shadow:
      0 10px 30px rgba(75, 46, 131, 0.10),
      0 8px 20px rgba(199, 120, 43, 0.06);
  }

  .feature-icon {
    font-size: 3rem;
    margin-bottom: 16px;
  }

  .feature-card h3 {
    font-family: 'Space Grotesk', sans-serif;
    font-size: 1.4rem;
    font-weight: 600;
    margin: 0 0 12px 0;
    color: var(--text-primary);
  }

  .feature-card p {
    font-size: 0.95rem;
    line-height: 1.6;
    color: var(--text-secondary);
    margin: 0;
  }

  /* Tablet and Mobile */
  @media (max-width: 1024px) {
    .hero {
      min-height: 100vh;
      padding: 100px 20px 40px;
    }

    .logo {
      width: 140px;
      height: 140px;
      margin-bottom: 24px;
    }

    h1 {
      font-size: 2.5rem;
      letter-spacing: -1px;
      margin-bottom: 16px;
    }

    .tagline {
      font-size: 1.1rem;
      margin-bottom: 12px;
    }

    .subtitle {
      font-size: 0.95rem;
      margin-bottom: 32px;
    }

    .cta-buttons {
      flex-direction: column;
      align-items: stretch;
      gap: 12px;
    }

    .btn {
      justify-content: center;
      padding: 12px 24px;
      font-size: 1rem;
    }

    .features {
      padding: 40px 16px;
    }

    .feature-grid {
      grid-template-columns: 1fr;
      gap: 20px;
    }

    .feature-card {
      padding: 24px;
    }

    .feature-icon {
      font-size: 2.5rem;
    }

    .feature-card h3 {
      font-size: 1.2rem;
    }

    .feature-card p {
      font-size: 0.9rem;
    }
  }

  /* Extra small devices */
  @media (max-width: 480px) {
    .hero {
      padding: 90px 16px 32px;
    }

    .logo {
      width: 120px;
      height: 120px;
      margin-bottom: 20px;
    }

    h1 {
      font-size: 2rem;
    }

    .tagline {
      font-size: 1rem;
    }

    .subtitle {
      font-size: 0.9rem;
    }

    .btn {
      font-size: 0.95rem;
      padding: 10px 20px;
    }

    .feature-card {
      padding: 20px;
    }
  }
</style>
