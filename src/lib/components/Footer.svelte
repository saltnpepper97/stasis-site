<script lang="ts">
  const currentYear = new Date().getFullYear();
</script>

<footer class="footer">
  <div class="footer-content">
    <div class="footer-section">
      <h3 class="footer-title">Stasis</h3>
      <p class="footer-description">
        A modern Wayland idle manager that knows when to step back.
      </p>
      <div class="social-links">
        <a href="https://github.com/saltnpepper97/stasis" target="_blank" rel="noopener noreferrer" aria-label="GitHub">
          <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="currentColor">
            <path d="M12 0c-6.626 0-12 5.373-12 12 0 5.302 3.438 9.8 8.207 11.387.599.111.793-.261.793-.577v-2.234c-3.338.726-4.033-1.416-4.033-1.416-.546-1.387-1.333-1.756-1.333-1.756-1.089-.745.083-.729.083-.729 1.205.084 1.839 1.237 1.839 1.237 1.07 1.834 2.807 1.304 3.492.997.107-.775.418-1.305.762-1.604-2.665-.305-5.467-1.334-5.467-5.931 0-1.311.469-2.381 1.236-3.221-.124-.303-.535-1.524.117-3.176 0 0 1.008-.322 3.301 1.23.957-.266 1.983-.399 3.003-.404 1.02.005 2.047.138 3.006.404 2.291-1.552 3.297-1.23 3.297-1.23.653 1.653.242 2.874.118 3.176.77.84 1.235 1.911 1.235 3.221 0 4.609-2.807 5.624-5.479 5.921.43.372.823 1.102.823 2.222v3.293c0 .319.192.694.801.576 4.765-1.589 8.199-6.086 8.199-11.386 0-6.627-5.373-12-12-12z"/>
          </svg>
        </a>
      </div>
    </div>

    <div class="footer-section">
      <h4>Documentation</h4>
      <ul>
        <li><a href="/quick-start">Quick Start</a></li>
        <li><a href="/configuration">Configuration</a></li>
        <li><a href="/integration">Integration</a></li>
        <li><a href="/faq">FAQ</a></li>
      </ul>
    </div>

    <div class="footer-section">
      <h4>Community</h4>
      <ul>
        <li><a href="https://github.com/saltnpepper97/stasis" target="_blank" rel="noopener noreferrer">GitHub</a></li>
        <li><a href="https://github.com/saltnpepper97/stasis/issues" target="_blank" rel="noopener noreferrer">Report Issues</a></li>
        <li><a href="/contributing">Contributing</a></li>
      </ul>
    </div>

    <div class="footer-section">
      <h4>Resources</h4>
      <ul>
        <li><a href="https://github.com/saltnpepper97/stasis/releases" target="_blank" rel="noopener noreferrer">Releases</a></li>
        <li><a href="https://aur.archlinux.org/packages/stasis" target="_blank" rel="noopener noreferrer">AUR Package</a></li>
        <li><a href="https://github.com/saltnpepper97/stasis/blob/main/LICENSE" target="_blank" rel="noopener noreferrer">License</a></li>
      </ul>
    </div>
  </div>

  <div class="footer-bottom">
    <p>© {currentYear} Stasis. Released under the MIT License.</p>
    <p class="made-with">Made with 💜 for the Wayland community</p>
  </div>
</footer>

<style>
  /* Footer background: calm suspended wash (cosmic violet + burnt core) */
  .footer {
    background:
      radial-gradient(900px 420px at 20% 20%,
        rgba(75, 46, 131, 0.10),
        transparent 60%),
      radial-gradient(900px 420px at 85% 30%,
        rgba(199, 120, 43, 0.08),
        transparent 60%),
      linear-gradient(180deg,
        rgba(75, 46, 131, 0.04),
        rgba(199, 120, 43, 0.02));
    border-top: 1px solid var(--border-color);
    padding: 60px 20px 20px;
    margin-top: auto;
  }

  .footer-content {
    max-width: 1200px;
    margin: 0 auto;
    display: grid;
    grid-template-columns: 2fr 1fr 1fr 1fr;
    gap: 48px;
    margin-bottom: 48px;
  }

  .footer-section h3 {
    font-family: 'Space Grotesk', sans-serif;
    font-size: 1.5rem;
    font-weight: 700;
    margin: 0 0 12px 0;

    /* Match logo gradient */
    background: linear-gradient(135deg, #4B2E83 0%, #C7782B 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
  }

  .footer-section h4 {
    font-family: 'Space Grotesk', sans-serif;
    font-size: 1.1rem;
    font-weight: 600;
    margin: 0 0 16px 0;
    color: var(--text-primary);
  }

  .footer-description {
    color: var(--text-secondary);
    line-height: 1.6;
    margin: 0 0 20px 0;
    max-width: 320px;
  }

  .social-links {
    display: flex;
    gap: 12px;
  }

  /* GitHub button: calm glass, icon stays black always */
  .social-links a {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 40px;
    height: 40px;

    background: rgba(200, 182, 255, 0.10);
    border: 1px solid var(--border-color);
    border-radius: 50%;

    color: #000; /* ALWAYS black */
    transition: transform 0.2s ease, background 0.2s ease, border-color 0.2s ease;
    backdrop-filter: blur(10px);
  }

  .social-links a:hover {
    background: rgba(200, 182, 255, 0.16);
    border-color: rgba(200, 182, 255, 0.35);
    transform: translateY(-2px);
  }

  .footer-section ul {
    list-style: none;
    padding: 0;
    margin: 0;
  }

  .footer-section li {
    margin-bottom: 12px;
  }

  .footer-section a {
    color: var(--text-secondary);
    text-decoration: none;
    transition: color 0.2s ease;
    font-size: 0.95rem;
  }

  .footer-section a:hover {
    color: var(--accent);
  }

  .footer-bottom {
    max-width: 1200px;
    margin: 0 auto;
    padding-top: 24px;
    border-top: 1px solid var(--border-color);
    text-align: center;
  }

  .footer-bottom p {
    color: var(--text-secondary);
    font-size: 0.9rem;
    margin: 8px 0;
  }

  .made-with {
    font-size: 0.85rem !important;
  }

  @media (max-width: 968px) {
    .footer {
      padding: 40px 20px 20px;
    }

    .footer-content {
      grid-template-columns: 1fr;
      gap: 32px;
      margin-bottom: 32px;
    }

    .footer-description {
      max-width: 100%;
    }
  }
</style>
