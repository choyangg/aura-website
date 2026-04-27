<footer>
	<div class="top-line"></div>
	<div class="container footer-inner">
		<div class="brand">
			<a href="/" class="logo">AURA</a>
			<p>Websites That Work While You Sleep.</p>
		</div>
		<div class="cols">
			<div class="col">
				<h4>Navigation</h4>
				<a href="/leistungen">Leistungen</a>
				<a href="/ablauf">Ablauf</a>
				<a href="/referenzen">Referenzen</a>
				<a href="/kontakt">Kontakt</a>
			</div>
			<div class="col">
				<h4>Kontakt</h4>
				<a href="mailto:information.auramarketing@gmail.com">information.auramarketing@gmail.com</a>
				<a href="tel:+41767020406">+41 76 702 04 06</a>
				<span>Schweiz · Mo–Fr 9–18</span>
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
	footer { background: #06060a; }
	.top-line { height: 1px; background: rgba(168,216,240,0.12); }
	.footer-inner {
		display: flex; justify-content: space-between; align-items: flex-start;
		gap: 4rem; padding: 5rem 0 4rem; flex-wrap: wrap;
	}
	.brand .logo {
		display: block; font-family: 'Playfair Display', Georgia, serif;
		font-size: 1.5rem; font-weight: 300; font-style: italic;
		letter-spacing: 0.28em; margin-bottom: 1rem;
		background: linear-gradient(
			105deg,
			#a8d8f0 0%, #a8d8f0 28%,
			#e8f8ff 44%, #ffffff 50%,
			#e8f8ff 56%, #a8d8f0 72%, #a8d8f0 100%
		);
		background-size: 300% auto;
		-webkit-background-clip: text;
		-webkit-text-fill-color: transparent;
		background-clip: text;
		animation: logoShimmer 4s ease-in-out infinite;
		filter: drop-shadow(0 0 12px rgba(168,216,240,0.15));
	}
	@keyframes logoShimmer {
		0%, 58%  { background-position: 0% center; }
		82%      { background-position: 150% center; }
		100%     { background-position: 0% center; }
	}
	.brand .logo:hover { filter: drop-shadow(0 0 24px rgba(168,216,240,0.4)); }
	.brand p { font-size: 0.82rem; color: rgba(190,215,235,0.75); line-height: 1.7; }
	.cols { display: flex; gap: 4rem; }
	.col { display: flex; flex-direction: column; gap: 0.65rem; }
	.col h4 {
		font-size: 0.62rem; font-weight: 600; letter-spacing: 0.2em;
		text-transform: uppercase; color: rgba(255,255,255,0.7); margin-bottom: 0.5rem;
	}
	.col a, .col span { font-size: 0.82rem; color: rgba(255,255,255,0.85); transition: color 0.2s; }
	.col a:hover { color: #a8d8f0; }
	.bottom { border-top: 1px solid rgba(255,255,255,0.08); }
	.bottom-inner {
		display: flex; align-items: center; justify-content: space-between;
		padding: 1.5rem 0; font-size: 0.72rem; color: rgba(255,255,255,0.5);
	}
	.legal { display: flex; gap: 1.5rem; }
	.legal a { color: rgba(255,255,255,0.5); transition: color 0.2s; }
	.legal a:hover { color: rgba(168,216,240,0.85); }
	@media (max-width: 768px) {
		.footer-inner { flex-direction: column; gap: 3rem; padding: 4rem 0 3rem; }
		.cols { gap: 2.5rem; }
		.bottom-inner { flex-direction: column; gap: 1rem; text-align: center; }
	}
</style>
