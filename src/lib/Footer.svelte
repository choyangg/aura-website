<footer>
	<div class="top-line"></div>
	<div class="container footer-inner">
		<div class="brand">
			<a href="/" class="logo">
				<img src="/logo.png" alt="AURA" />
			</a>
			<p>Erfolg, der niemals schläft.</p>
		</div>
		<div class="cols">
			<div class="col">
				<h4>Navigation</h4>
				<a href="/leistungen">Leistungen</a>
				<a href="/ablauf">Ablauf</a>
				<a href="/referenzen">Projekte</a>
				<a href="/kontakt">Kontakt</a>
			</div>
			<div class="col">
				<h4>Kontakt</h4>
				<a href="mailto:information.auramarketing@gmail.com">information.auramarketing@gmail.com</a>
				<a href="tel:+41767020406">+41 76 702 04 06</a>
				<span>Schweiz · Di bis So · 9 bis 18 Uhr</span>
			</div>
		</div>
	</div>
	<div class="bottom">
		<div class="container bottom-inner">
			<span>© 2026 AURA Web Studio. All rights reserved.</span>
			<div class="legal">
				<a href="/datenschutz">Datenschutz</a>
				<a href="/impressum">Impressum</a>
			</div>
		</div>
	</div>
</footer>

<style>
	footer { background: #06060a; width: 100%; }
	.top-line { height: 1px; background: rgba(168,216,240,0.12); }
	.footer-inner {
		display: flex; justify-content: space-between; align-items: flex-start;
		gap: 4rem; padding-top: 5rem; padding-bottom: 4rem; flex-wrap: wrap;
	}
	.brand .logo {
		display: inline-flex; margin-bottom: 1rem;
		filter: drop-shadow(0 0 10px rgba(168,216,240,0.15));
		transition: filter 0.3s;
	}
	.brand .logo img { height: 52px; width: auto; display: block; }
	.brand .logo:hover { filter: drop-shadow(0 0 22px rgba(168,216,240,0.4)); }
	.brand p { font-size: 0.82rem; color: rgba(190,215,235,0.75); line-height: 1.7; }
	.cols { display: flex; gap: 4rem; }
	.col { display: flex; flex-direction: column; gap: 0.65rem; }
	.col h4 {
		font-size: 0.62rem; font-weight: 600; letter-spacing: 0.2em;
		text-transform: uppercase; color: rgba(255,255,255,0.7); margin-bottom: 0.5rem;
	}
	.col a, .col span {
		font-size: 0.82rem; color: rgba(255,255,255,0.85); transition: color 0.2s;
		word-break: break-word; overflow-wrap: break-word;
	}
	.col a:hover { color: #a8d8f0; }
	.bottom { border-top: 1px solid rgba(255,255,255,0.08); }
	.bottom-inner {
		display: flex; align-items: center; justify-content: space-between;
		padding: 1.5rem 0; font-size: 0.72rem; color: rgba(255,255,255,0.5);
	}
	.legal { display: flex; gap: 1.5rem; }
	.legal a { color: rgba(255,255,255,0.5); transition: color 0.2s; }
	.legal a:hover { color: rgba(168,216,240,0.85); }
	@media (max-width: 900px) {
		.footer-inner { gap: 2.5rem; }
		.cols { gap: 2rem; }
	}
	@media (max-width: 768px) {
		.footer-inner { flex-direction: column; gap: 2.5rem; padding: 4rem 0 3rem; }
		.cols { gap: 2rem; flex-wrap: wrap; }
		.bottom-inner { flex-direction: column; gap: 1rem; text-align: center; }
	}
	@media (max-width: 480px) {
		.cols { flex-direction: column; gap: 2rem; }
		.footer-inner { padding: 3rem 0 2.5rem; }
	}
</style>
