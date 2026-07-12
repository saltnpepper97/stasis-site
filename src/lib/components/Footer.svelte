<script lang="ts">
  import { resolve } from '$app/paths';
  const currentYear = new Date().getFullYear();
</script>

<footer class="footer">
  <div class="footer-content">
    <div class="footer-section">
      <h3>Stasis</h3>
      <p class="footer-description">A deliberate Wayland idle manager with ordered actions and clear state.</p>
    </div>
    <div class="footer-section">
      <h4>Read</h4>
      <ul>
        <li><a href={resolve('/quick-start')}>Quick start</a></li>
        <li><a href={resolve('/configuration')}>Configuration</a></li>
        <li><a href={resolve('/integration')}>Integration</a></li>
      </ul>
    </div>
    <div class="footer-section">
      <h4>Project</h4>
      <ul>
        <li><a href="https://github.com/saltnpepper97/stasis">GitHub</a></li>
        <li><a href="https://github.com/saltnpepper97/stasis/releases">Releases</a></li>
        <li><a href={resolve('/contributing')}>Contribute</a></li>
      </ul>
    </div>
    <div class="footer-section">
      <h4>Support</h4>
      <ul>
        <li><a href={resolve('/faq')}>FAQ</a></li>
        <li><a href="https://github.com/saltnpepper97/stasis/issues">Report an issue</a></li>
        <li><a href="https://aur.archlinux.org/packages/stasis">AUR package</a></li>
      </ul>
    </div>
  </div>
  <div class="footer-bottom">
    <p>© {currentYear} Stasis. GNU GPLv3.</p>
    <p>Made for Wayland sessions.</p>
  </div>
</footer>
