<script>
	let cursorDot = $state(null);
	let cursorRing = $state(null);
	let scrolled = $state(false);
	let menuOpen = $state(false);

	const projs = [
		{ src: '/projekte/lumina_website_no_frame.png' },
		{ src: '/projekte/physio_final_v3.png' },
		{ src: '/projekte/website_only.png' },
		{ src: '/projekte/ChatGPT Image 27. Apr. 2026, 21_00_33 - Edited.png' },
	];
	let projClip = $state(null);
	let projIndex = $state(0);
	let lbOpen  = $state(false);
	let lbIndex = $state(0);

	function openLb(i) { lbIndex = i; lbOpen = true; }
	function closeLb()  { lbOpen = false; }
	function lbNext()   { lbIndex = (lbIndex + 1) % projs.length; }
	function lbPrev()   { lbIndex = (lbIndex - 1 + projs.length) % projs.length; }

	$effect(() => {
		if (!lbOpen) return;
		const onKey = (e) => {
			if (e.key === 'Escape')      closeLb();
			if (e.key === 'ArrowRight')  lbNext();
			if (e.key === 'ArrowLeft')   lbPrev();
		};
		window.addEventListener('keydown', onKey);
		return () => window.removeEventListener('keydown', onKey);
	});

	function projScroll(dir) {
		if (!projClip) return;
		projClip.scrollBy({ left: dir * (projClip.offsetWidth / 3 + 8), behavior: 'smooth' });
	}
	function onProjScroll() {
		if (!projClip) return;
		const max = projClip.scrollWidth - projClip.clientWidth;
		projIndex = max > 0 ? Math.min(Math.round((projClip.scrollLeft / max) * (projs.length - 1)), projs.length - 1) : 0;
	}
	function scrollToSlide(i) {
		if (!projClip) return;
		projClip.scrollTo({ left: (projClip.scrollWidth / projs.length) * i, behavior: 'smooth' });
	}

	const links = [
		{ href: '/leistungen',  label: 'Leistungen'  },
		{ href: '/ablauf',      label: 'Ablauf'      },
		{ href: '/referenzen',  label: 'Projekte'  },
	];

	$effect(() => {
		if (typeof window === 'undefined') return;

		let mx = window.innerWidth / 2, my = window.innerHeight / 2;
		let rx = mx, ry = my;
		let raf;

		const moveCursor = (e) => { mx = e.clientX; my = e.clientY; };
		window.addEventListener('pointermove', moveCursor);
		const hoverEls = document.querySelectorAll('a, button');
		hoverEls.forEach(el => {
			el.addEventListener('mouseenter', () => cursorRing?.classList.add('expand'));
			el.addEventListener('mouseleave', () => cursorRing?.classList.remove('expand'));
		});
		function loop() {
			rx += (mx - rx) * 0.12; ry += (my - ry) * 0.12;
			if (cursorDot)  { cursorDot.style.transform  = `translate(${mx - 4}px, ${my - 4}px)`; }
			if (cursorRing) { cursorRing.style.transform = `translate(${rx - 20}px, ${ry - 20}px)`; }
			raf = requestAnimationFrame(loop);
		}
		raf = requestAnimationFrame(loop);

		const onScroll = () => { scrolled = window.scrollY > 60; };
		window.addEventListener('scroll', onScroll, { passive: true });
		onScroll();

		const revealEls = document.querySelectorAll('[data-reveal]');
		revealEls.forEach((el) => {
			const delay = +(el.dataset.delay || 0);
			el.style.opacity = '0';
			el.style.transform = 'translateY(60px) scale(0.98)';
			el.style.transition = `opacity 1s cubic-bezier(0.16,1,0.3,1) ${delay}ms, transform 1s cubic-bezier(0.16,1,0.3,1) ${delay}ms`;
		});
		const revealIO = new IntersectionObserver((entries) => {
			entries.forEach(entry => {
				if (!entry.isIntersecting) return;
				entry.target.style.opacity = '1';
				entry.target.style.transform = 'translateY(0) scale(1)';
				revealIO.unobserve(entry.target);
			});
		}, { threshold: 0.08 });
		revealEls.forEach(el => revealIO.observe(el));

		return () => {
			window.removeEventListener('pointermove', moveCursor);
			window.removeEventListener('scroll', onScroll);
			cancelAnimationFrame(raf);
			revealIO.disconnect();
		};
	});
</script>

<div class="cursor-dot" bind:this={cursorDot}></div>
<div class="cursor-ring" bind:this={cursorRing}></div>

<!-- ══ HOME NAV ══ -->
<nav class="home-nav" class:scrolled>
	<div class="container nav-inner">
		<a href="/" class="logo" onclick={() => menuOpen = false}>
			<img src="/logo.png" alt="AURA" />
		</a>
		<div class="links" class:open={menuOpen}>
			{#each links as l}
				<a href={l.href} onclick={() => menuOpen = false}>{l.label}</a>
			{/each}
			<a href="/kontakt" class="nav-cta" onclick={() => menuOpen = false}>Projekt starten →</a>
		</div>
		<button class="burger" onclick={() => menuOpen = !menuOpen} aria-label="Menu">
			<span class:x={menuOpen}></span>
		</button>
	</div>
</nav>
{#if menuOpen}
	<div class="backdrop" onclick={() => menuOpen = false} role="presentation"></div>
{/if}

<!-- ══ HERO + STATS (zusammen 100vh) ══ -->
<div class="first-screen">
<section class="hero">

	<div class="aurora">
		<div class="orb o1"></div>
		<div class="orb o2"></div>
		<div class="orb o3"></div>
		<div class="orb o4"></div>
		<div class="orb o5"></div>
	</div>

	<div class="stars" aria-hidden="true">
		{#each Array(80) as _, i}
			{@const dx = ((i * 7 + 3) % 30) - 15}
			{@const dy = ((i * 11 + 5) % 20) - 10}
			<div
				class="star"
				class:star-mid={i % 5 === 0}
				class:star-bright={i % 7 === 0}
				style="
					left:{(i*3.9+(i%11)*4.7+1.5)%98}%;
					top:{(i*4.3+(i%7)*5.1+2)%96}%;
					animation-duration:{2.5+(i%9)*0.6}s;
					animation-delay:-{(i*0.41)%6}s;
					--dx:{dx}px;--dy:{dy}px;
				"
			></div>
		{/each}
	</div>

	<div class="shoots" aria-hidden="true">
		{#each Array(12) as _, i}
			<div class="shoot" style="--d:{i*3.5+1}s;--top:{4+i*8}%;--left:{5+i*8}%;"></div>
		{/each}
	</div>

	<div class="grain"></div>
	<div class="hero-watermark" aria-hidden="true">AURA</div>

	<div class="container hero-inner">

		<div class="hero-left">
			<h1 class="hero-heading">
				<span class="reveal-line">
					<span class="reveal-word" style="animation-delay:0.25s">Erfolg, der niemals schläft.</span>
				</span>
				<span class="reveal-line accent-line">
					<span class="reveal-word accent" style="animation-delay:0.45s">Ihre Website macht's möglich.</span>
				</span>
			</h1>

			<div class="hero-foot">
				<div class="hero-accent-line"></div>
				<p class="hero-sub">
					Massgeschneiderte Websites für Schweizer Unternehmen.
				</p>
				<div class="hero-actions">
					<a href="/kontakt" class="btn-ice">Projekt starten →</a>
					<a href="/referenzen" class="hero-link">Arbeiten ansehen ↓</a>
				</div>
			</div>
		</div>

		<div class="hero-stats">
			<div class="hstat">
				<span class="hstat-num">50+</span>
				<span class="hstat-label">Projekte realisiert</span>
			</div>
			<div class="hstat-sep"></div>
			<div class="hstat">
				<span class="hstat-num">100%</span>
				<span class="hstat-label">Schweizer Qualität</span>
			</div>
			<div class="hstat-sep"></div>
			<div class="hstat">
				<span class="hstat-num">2 Wo.</span>
				<span class="hstat-label">bis Ihre Website live ist</span>
			</div>
		</div>

	</div>

</section>
</div>

<!-- ══ PHILOSOPHIE (hell) ══ -->
<section class="philosophy">
	<div class="container">
		<div class="philo-top" data-reveal>
			<span class="philo-eyebrow">Unsere Überzeugung</span>
			<p class="philo-text">
				Eine Website ist kein Projekt.<br/>
				Sie ist Ihr <em>stärkster Verkäufer</em>.
			</p>
		</div>
		<div class="philo-bottom" data-reveal>
			<div class="philo-lead">
				<p class="philo-lead-title">Was wir für Sie möglich machen.</p>
				<p class="philo-lead-sub">Wir bauen Websites, die Vertrauen schaffen, überzeugen und messbar Anfragen generieren.</p>
			</div>
			<div class="philo-cards">
				<div class="philo-card">
					<span class="philo-card-icon">
						<svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><rect x="3" y="4" width="18" height="18" rx="2" ry="2"/><line x1="16" y1="2" x2="16" y2="6"/><line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/></svg>
					</span>
					<h3>Online-Terminbuchung</h3>
					<p>Ihre Kunden buchen direkt auf Ihrer Website. Rund um die Uhr, ohne Anruf, ohne Wartezeit. Voller Kalender, weniger Aufwand.</p>
				</div>
				<div class="philo-card">
					<span class="philo-card-icon">
						<svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><path d="M11 4H4a2 2 0 00-2 2v14a2 2 0 002 2h14a2 2 0 002-2v-7"/><path d="M18.5 2.5a2.121 2.121 0 013 3L12 15l-4 1 1-4 9.5-9.5z"/></svg>
					</span>
					<h3>Selbst anpassen, wann Sie wollen</h3>
					<p>Öffnungszeiten, Angebote, Bilder. Sie aktualisieren Ihre Website selbst. Einfach, schnell, ohne technisches Wissen. Keine Agentur, kein Warten.</p>
				</div>
				<div class="philo-card">
					<span class="philo-card-icon">
						<svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="22 12 18 12 15 21 9 3 6 12 2 12"/></svg>
					</span>
					<h3>Digitale Entlastung</h3>
					<p>Ihre Website arbeitet auch wenn Sie schlafen. Sie antwortet, informiert und überzeugt. Weniger Aufwand für Sie, mehr Infos für Ihre Kunden.</p>
				</div>
			</div>
		</div>
	</div>
</section>

<!-- ══ PROCESS ══ -->
<section class="section section-soft" id="ablauf">
	<div class="container">
		<div class="section-header" data-reveal>
			<span class="eyebrow">Ablauf</span>
			<h2 class="section-title">Von der Idee zur<br/><em>fertigen Website.</em></h2>
		</div>
		<div class="process-steps">
			<div class="process-line"></div>
			{#each [
				{ n: '01', title: 'Gespräch',  desc: 'Wir lernen Ihr Unternehmen kennen und verstehen Ihre Ziele.' },
				{ n: '02', title: 'Konzept',   desc: 'Wir erstellen ein massgeschneidertes Design-Konzept für Sie.' },
				{ n: '03', title: 'Umsetzung', desc: 'Wir bauen Ihre Website. Präzise, schnell und responsiv.' },
				{ n: '04', title: 'Launch',    desc: 'Ihre Website geht live. Wir sind weiterhin für Sie da.' },
			] as step, i}
				<div class="process-step" data-reveal data-delay={i * 100}>
					<div class="step-num">{step.n}</div>
					<h3 class="step-title">{step.title}</h3>
					<p class="step-desc">{step.desc}</p>
				</div>
			{/each}
		</div>
	</div>
</section>

<!-- ══ PROJEKTE (hell) ══ -->
<section class="section proj-light">
	<div class="container">
		<div class="section-header" data-reveal>
			<span class="eyebrow proj-eyebrow">Unsere Arbeiten</span>
			<h2 class="section-title proj-title">Websites, die wir<br/><em>gebaut haben.</em></h2>
		</div>
	</div>
	<div class="proj-wide" data-reveal>
		<div class="proj-stage">
			<button class="proj-arrow proj-prev" onclick={() => projScroll(-1)} aria-label="Zurück">
				<svg width="16" height="16" viewBox="0 0 16 16" fill="none"><path d="M10 12L6 8l4-4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></svg>
			</button>
			<div class="proj-clip" bind:this={projClip} onscroll={onProjScroll}>
				{#each projs as p, i}
					<button class="proj-slide" onclick={() => openLb(i)}>
						<div class="proj-img-wrap">
							<img src={p.src} alt="Projekt" loading="lazy" />
						</div>
					</button>
				{/each}
			</div>
			<button class="proj-arrow proj-next" onclick={() => projScroll(1)} aria-label="Weiter">
				<svg width="16" height="16" viewBox="0 0 16 16" fill="none"><path d="M6 4l4 4-4 4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></svg>
			</button>
		</div>
		<div class="proj-footer">
			<div class="proj-dots">
				{#each projs as _, i}
					<button class="proj-dot" class:active={projIndex === i} onclick={() => scrollToSlide(i)} aria-label="Slide {i+1}"></button>
				{/each}
			</div>
			<span class="proj-counter">{projIndex + 1} / {projs.length}</span>
			<a href="/referenzen" class="proj-link">Projekte →</a>
		</div>
	</div>
</section>

<!-- ══ LIGHTBOX ══ -->
{#if lbOpen}
<div class="lb-overlay" onclick={closeLb} role="dialog" aria-modal="true">
	<button class="lb-close" onclick={closeLb} aria-label="Schliessen">✕</button>
	<button class="lb-nav lb-prev" onclick={(e) => { e.stopPropagation(); lbPrev(); }} aria-label="Vorheriges Bild">
		<svg width="20" height="20" viewBox="0 0 16 16" fill="none"><path d="M10 12L6 8l4-4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></svg>
	</button>
	<div class="lb-img-wrap" onclick={(e) => e.stopPropagation()}>
		<img src={projs[lbIndex].src} alt="Projekt {lbIndex + 1}" />
	</div>
	<button class="lb-nav lb-next" onclick={(e) => { e.stopPropagation(); lbNext(); }} aria-label="Nächstes Bild">
		<svg width="20" height="20" viewBox="0 0 16 16" fill="none"><path d="M6 4l4 4-4 4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></svg>
	</button>
	<span class="lb-counter">{lbIndex + 1} / {projs.length}</span>
</div>
{/if}

<!-- ══ FOOTER ══ -->
<footer class="home-footer">
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
	/* ── Cursor ── */
	.cursor-dot, .cursor-ring {
		position: fixed; top: 0; left: 0; pointer-events: none; z-index: 9999; will-change: transform;
	}
	.cursor-dot { width: 8px; height: 8px; border-radius: 50%; background: #a8d8f0; }
	.cursor-ring {
		width: 40px; height: 40px; border-radius: 50%;
		border: 1px solid rgba(168,216,240,0.5);
		transition: width 0.25s, height 0.25s, border-color 0.25s;
	}
	:global(.cursor-ring.expand) { width: 60px; height: 60px; border-color: #a8d8f0; }
	@media (pointer: coarse) { .cursor-dot, .cursor-ring { display: none; } }

	/* ── Nav ── */
	.home-nav {
		position: fixed; top: 0; left: 0; right: 0; z-index: 300;
		transition: background 0.5s, padding 0.4s, border-color 0.4s;
		padding: 1.75rem 0; border-bottom: 1px solid transparent;
	}
	.home-nav.scrolled {
		background: rgba(4,4,10,0.96);
		backdrop-filter: blur(28px) saturate(180%);
		padding: 1rem 0;
		border-bottom-color: rgba(168,216,240,0.1);
	}
	.nav-inner { display: flex; align-items: center; }

	/* ── Logo ── */
	.logo {
		flex: 1; display: flex; align-items: center;
		filter: drop-shadow(0 0 14px rgba(168,216,240,0.18));
		transition: filter 0.3s;
	}
	.logo img { height: 52px; width: auto; display: block; }
	.logo:hover { filter: drop-shadow(0 0 28px rgba(168,216,240,0.5)); }

	.links { display: flex; align-items: center; gap: 0.25rem; }
	.links a:not(.nav-cta) {
		padding: 0.5rem 0.875rem; font-size: 0.72rem; font-weight: 400;
		letter-spacing: 0.12em; text-transform: uppercase;
		color: rgba(255,255,255,0.65); transition: color 0.2s;
	}
	.links a:not(.nav-cta):hover { color: #fff; }
	.nav-cta {
		margin-left: 1.25rem; padding: 0.6rem 1.6rem;
		background: rgba(168,216,240,0.12);
		border: 1px solid rgba(168,216,240,0.55); color: #e0f4ff;
		font-size: 0.68rem; font-weight: 600; letter-spacing: 0.14em;
		text-transform: uppercase; transition: all 0.25s; border-radius: 2px;
	}
	.nav-cta:hover { background: rgba(168,216,240,0.22); border-color: #a8d8f0; color: #fff; box-shadow: 0 0 20px rgba(168,216,240,0.15); }
	.burger {
		display: none; background: none; border: none; cursor: pointer;
		padding: 0.5rem; flex-direction: column; justify-content: center; gap: 5px;
	}
	.burger span, .burger span::before, .burger span::after {
		display: block; width: 22px; height: 1px; background: #a8d8f0;
		transition: all 0.3s; position: relative;
	}
	.burger span::before, .burger span::after { content: ''; position: absolute; }
	.burger span::before { top: -6px; }
	.burger span::after  { top: 6px; }
	.burger span.x { background: transparent; }
	.burger span.x::before { top: 0; transform: rotate(45deg); }
	.burger span.x::after  { top: -2px; transform: rotate(-45deg); }
	.backdrop { position: fixed; inset: 0; background: rgba(4,4,10,0.65); z-index: 290; backdrop-filter: blur(4px); }

	/* ══════════════════════════════
	   HERO
	══════════════════════════════ */
	.hero {
		min-height: 90vh; display: flex; align-items: center;
		background: #04040c; position: relative; overflow: hidden;
	}

	/* ── Aurora orbs ── */
	.aurora { position: absolute; inset: 0; z-index: 0; }
	.orb { position: absolute; border-radius: 50%; filter: blur(120px); will-change: transform; }
	.o1 {
		width: 700px; height: 700px;
		background: radial-gradient(ellipse, rgba(56,189,248,0.6) 0%, rgba(14,165,233,0.18) 55%, transparent 72%);
		top: -20%; left: -10%; animation: orbA 14s ease-in-out infinite alternate;
	}
	.o2 {
		width: 600px; height: 600px;
		background: radial-gradient(ellipse, rgba(37,99,235,0.65) 0%, rgba(29,78,216,0.18) 55%, transparent 72%);
		bottom: -22%; right: -6%; animation: orbB 20s ease-in-out infinite alternate;
	}
	.o3 {
		width: 520px; height: 520px;
		background: radial-gradient(ellipse, rgba(124,58,237,0.55) 0%, rgba(109,40,217,0.16) 55%, transparent 72%);
		top: 5%; right: 8%; animation: orbC 17s ease-in-out infinite alternate;
	}
	.o4 {
		width: 480px; height: 480px;
		background: radial-gradient(ellipse, rgba(6,182,212,0.4) 0%, rgba(8,145,178,0.12) 55%, transparent 72%);
		top: 35%; left: 12%; animation: orbD 24s ease-in-out infinite alternate;
	}
	.o5 {
		width: 400px; height: 400px;
		background: radial-gradient(ellipse, rgba(168,85,247,0.38) 0%, rgba(139,92,246,0.1) 55%, transparent 72%);
		bottom: 8%; left: 38%; animation: orbE 19s ease-in-out infinite alternate;
	}
	@keyframes orbA {
		0%   { transform: translate(0,0) scale(1); }
		35%  { transform: translate(70px, 55px) scale(1.1); }
		70%  { transform: translate(-40px, 90px) scale(0.93); }
		100% { transform: translate(90px,-65px) scale(1.14); }
	}
	@keyframes orbB {
		0%   { transform: translate(0,0) scale(1); }
		40%  { transform: translate(-90px,-70px) scale(1.12); }
		75%  { transform: translate(55px,-100px) scale(0.9); }
		100% { transform: translate(-70px, 80px) scale(1.08); }
	}
	@keyframes orbC {
		0%   { transform: translate(0,0) scale(1); }
		30%  { transform: translate(-80px, 60px) scale(1.08); }
		65%  { transform: translate(60px, 85px) scale(0.88); }
		100% { transform: translate(-55px,-70px) scale(1.12); }
	}
	@keyframes orbD {
		0%   { transform: translate(0,0) scale(1); }
		45%  { transform: translate(100px,-80px) scale(1.15); }
		100% { transform: translate(-50px, 70px) scale(0.92); }
	}
	@keyframes orbE {
		0%   { transform: translate(0,0) scale(1); }
		50%  { transform: translate(-70px,-60px) scale(1.2); }
		100% { transform: translate(80px, 50px) scale(0.88); }
	}

	/* ── Stars ── */
	.stars { position: absolute; inset: 0; z-index: 2; pointer-events: none; overflow: hidden; }
	.star {
		position: absolute; border-radius: 50%;
		width: 1.5px; height: 1.5px;
		background: rgba(220,240,255,0.7);
		animation: starFloat ease-in-out infinite alternate;
	}
	.star-mid {
		width: 2.5px; height: 2.5px;
		background: rgba(220,240,255,0.85);
		box-shadow: 0 0 4px rgba(168,216,240,0.6);
		animation-name: starFloatMid;
	}
	.star-bright {
		width: 3.5px; height: 3.5px;
		background: #fff;
		box-shadow: 0 0 6px 2px rgba(168,216,240,0.8), 0 0 14px rgba(168,216,240,0.3);
		animation-name: starFloatBright;
	}
	@keyframes starFloat {
		from { opacity: 0.15; transform: translate(0,0) scale(1); }
		to   { opacity: 0.6;  transform: translate(var(--dx,0px),var(--dy,0px)) scale(1.25); }
	}
	@keyframes starFloatMid {
		from { opacity: 0.3; transform: translate(0,0) scale(1); }
		to   { opacity: 0.9; transform: translate(var(--dx,0px),var(--dy,0px)) scale(1.35); }
	}
	@keyframes starFloatBright {
		from { opacity: 0.5; transform: translate(0,0) scale(1); box-shadow: 0 0 6px 2px rgba(168,216,240,0.5); }
		to   { opacity: 1;   transform: translate(var(--dx,0px),var(--dy,0px)) scale(1.4); box-shadow: 0 0 12px 3px rgba(168,216,240,0.9), 0 0 24px rgba(168,216,240,0.4); }
	}

	/* ── Shooting stars ── */
	.shoots { position: absolute; inset: 0; z-index: 3; pointer-events: none; overflow: hidden; }
	.shoot {
		position: absolute;
		top: var(--top); left: var(--left);
		width: 130px; height: 1px;
		background: linear-gradient(to right, rgba(168,216,240,0.95), transparent);
		transform: rotate(32deg);
		transform-origin: left center;
		opacity: 0;
		animation: shootAnim 9s ease-in infinite;
		animation-delay: var(--d);
	}
	@keyframes shootAnim {
		0%   { opacity: 0;   transform: rotate(32deg) translateX(-60px); }
		4%   { opacity: 0.9; }
		22%  { opacity: 0;   transform: rotate(32deg) translateX(400px); }
		100% { opacity: 0;   transform: rotate(32deg) translateX(400px); }
	}

	/* ── Grain ── */
	.grain {
		position: absolute; inset: 0; z-index: 3; pointer-events: none; opacity: 0.28;
		background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='200' height='200'%3E%3Cfilter id='g'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.75' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='200' height='200' filter='url(%23g)' opacity='1'/%3E%3C/svg%3E");
		background-size: 200px 200px;
	}

	/* ── Watermark ── */
	.hero-watermark {
		position: absolute; z-index: 1; pointer-events: none; user-select: none;
		font-family: 'Playfair Display', Georgia, serif;
		font-size: clamp(18rem, 30vw, 34rem);
		font-weight: 700; font-style: italic;
		color: transparent;
		-webkit-text-stroke: 1px rgba(168,216,240,0.045);
		right: -6%; bottom: -12%; line-height: 1; letter-spacing: -0.03em;
		animation: watermarkDrift 20s ease-in-out infinite alternate;
	}
	@keyframes watermarkDrift {
		from { transform: translate(0, 0); }
		to   { transform: translate(-20px, -15px); }
	}

	/* ── Hero content ── */
	.hero-inner {
		position: relative; z-index: 4;
		padding-top: 5rem; padding-bottom: 0;
		width: 100%;
		display: flex; align-items: stretch; justify-content: space-between; gap: 4rem;
	}
	.hero-left { flex: 1; min-width: 0; padding-bottom: 3rem; }

	/* ── Word reveal heading ── */
	.hero-heading {
		margin: 0 0 2rem;
		font-family: 'Playfair Display', Georgia, serif;
		font-size: clamp(1.9rem, 3.8vw, 4rem);
		font-weight: 600; letter-spacing: -0.03em; line-height: 1.05;
	}
	.reveal-line {
		display: block; overflow: hidden; padding-bottom: 0.08em;
	}
	.accent-line { padding-bottom: 0.12em; }
	.reveal-word {
		display: block; color: #f0f4f8;
		transform: translateY(110%);
		animation: wordUp 1.1s cubic-bezier(0.16,1,0.3,1) both;
	}
	.reveal-word.accent {
		font-style: italic; color: #7ec8e3;
		font-size: clamp(1.4rem, 3.4vw, 3.7rem);
		text-shadow: 0 0 80px rgba(56,189,248,0.4), 0 0 160px rgba(126,200,227,0.15);
	}
	@keyframes wordUp { to { transform: translateY(0); } }

	/* ── Hero foot ── */
	.hero-foot {
		opacity: 0; animation: footIn 1s ease 0.85s both;
		max-width: 520px;
	}
	@keyframes footIn { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: none; } }
	.hero-accent-line {
		width: 0; height: 1px;
		background: rgba(168,216,240,0.3);
		margin-bottom: 1.75rem;
		animation: lineGrow 0.9s cubic-bezier(0.16,1,0.3,1) 1.1s both;
	}
	@keyframes lineGrow { to { width: 48px; } }
	.hero-sub {
		font-size: clamp(0.9rem, 1.3vw, 1.05rem);
		color: rgba(200,220,240,0.82); line-height: 1.9; margin-bottom: 2rem;
	}
	.hero-actions { display: flex; gap: 1.25rem; flex-wrap: wrap; align-items: center; }
	.hero-link {
		font-size: 0.68rem; font-weight: 500; letter-spacing: 0.16em;
		text-transform: uppercase; color: rgba(168,216,240,0.65); transition: color 0.2s;
	}
	.hero-link:hover { color: #a8d8f0; }
	/* ── Button ── */
	.btn-ice {
		display: inline-flex; align-items: center; gap: 0.5rem;
		padding: 1rem 2.75rem;
		background: rgba(168,216,240,0.14);
		border: 1px solid rgba(168,216,240,0.6);
		color: #e0f4ff; font-size: 0.72rem; font-weight: 600;
		letter-spacing: 0.18em; text-transform: uppercase;
		transition: all 0.3s cubic-bezier(0.16,1,0.3,1);
		border-radius: 2px; position: relative; overflow: hidden;
	}
	.btn-ice::after {
		content: ''; position: absolute; top: 0; left: -120%; width: 55%; height: 100%;
		background: linear-gradient(90deg, transparent, rgba(168,216,240,0.5), transparent);
		animation: btnShimmer 3s ease-in-out infinite;
	}
	@keyframes btnShimmer {
		0%        { left: -120%; }
		20%       { left: 130%; }
		20.01%,100% { left: -120%; }
	}
	.btn-ice:hover {
		background: rgba(168,216,240,0.25); border-color: #a8d8f0; color: #fff;
		box-shadow: 0 0 40px rgba(56,189,248,0.25), 0 0 80px rgba(126,200,227,0.12);
		transform: translateY(-2px);
	}
	.btn-ice:hover::after { animation: btnShimmerFast 0.5s ease forwards; }
	@keyframes btnShimmerFast { from { left: -120%; } to { left: 130%; } }

	/* ── First Screen Wrapper ── */
	.first-screen { position: relative; }

	/* ── Hero stats (right column) ── */
	.hero-stats {
		display: flex; flex-direction: column; gap: 0;
		align-self: flex-end; padding-bottom: 3rem;
		text-align: right; flex-shrink: 0; width: 28%;
	}
	.hstat { padding: 1.5rem 0; }
	.hstat-num {
		display: block;
		font-family: 'Playfair Display', Georgia, serif;
		font-size: clamp(1.9rem, 2.8vw, 2.6rem);
		font-weight: 600; color: rgba(240,244,248,0.88);
		letter-spacing: -0.02em; line-height: 1;
		margin-bottom: 0.4rem;
	}
	.hstat-label {
		display: block;
		font-size: 0.66rem; font-weight: 400; letter-spacing: 0.16em;
		text-transform: uppercase; color: rgba(255,255,255,0.4);
	}
	.hstat-sep { height: 1px; background: rgba(168,216,240,0.06); }

	/* ── Philosophie ── */
	.philosophy { background: #f2f0ec; padding: 7rem 0; }
	.philo-top { margin-bottom: 4rem; }
	.philo-eyebrow {
		display: inline-block; font-size: 0.62rem; font-weight: 600;
		letter-spacing: 0.22em; text-transform: uppercase;
		color: rgba(14,14,22,0.5); margin-bottom: 2rem;
	}
	.philo-text {
		font-family: 'Playfair Display', Georgia, serif;
		font-size: clamp(2rem, 4vw, 3.25rem);
		font-weight: 600; line-height: 1.25; letter-spacing: -0.02em;
		color: #0e0e16; margin-bottom: 0;
	}
	.philo-text em { font-style: italic; color: #1a4a6b; }

	.philo-lead { margin-bottom: 2.5rem; max-width: 680px; }
	.philo-lead-title {
		font-family: 'Cormorant Garamond', Georgia, serif;
		font-size: clamp(1.7rem, 3vw, 2.6rem);
		font-weight: 400; color: #0e0e16;
		letter-spacing: 0.01em; line-height: 1.3; margin-bottom: 0.875rem;
	}
	.philo-lead-sub { font-size: 1.05rem; color: rgba(14,14,22,0.58); line-height: 1.8; white-space: nowrap; }

	.philo-cards { display: grid; grid-template-columns: repeat(3, 1fr); gap: 1.25rem; }
	.philo-card {
		background: #fff; border-top: 2px solid #1a4a6b; border-radius: 6px;
		padding: 2rem 1.75rem;
		box-shadow: 0 2px 16px rgba(14,14,22,0.06);
		transition: box-shadow 0.3s, transform 0.3s;
	}
	.philo-card:hover {
		box-shadow: 0 6px 32px rgba(26,74,107,0.18), 0 2px 16px rgba(14,14,22,0.08);
		transform: translateY(-3px);
	}
	.philo-card-icon { display: block; color: #1a4a6b; margin-bottom: 1.25rem; }
	.philo-card h3 {
		font-size: 1rem; font-weight: 600; color: #0e0e16;
		margin-bottom: 0.75rem; letter-spacing: -0.01em;
	}
	.philo-card p { font-size: 0.875rem; color: rgba(14,14,22,0.62); line-height: 1.8; }

	/* ── Sections ── */
	.section { padding: 9rem 0; }
	.section-soft  { background: #0e0e16; }
	.section-header { margin-bottom: 5rem; }
	.eyebrow {
		display: inline-block; font-size: 0.62rem; font-weight: 500;
		letter-spacing: 0.28em; text-transform: uppercase; color: #a8d8f0; margin-bottom: 1.5rem;
	}
	.section-title {
		font-family: 'Playfair Display', Georgia, serif;
		font-size: clamp(2.2rem, 4vw, 3.5rem);
		font-weight: 600; letter-spacing: -0.025em; line-height: 1.15; color: #f0f4f8;
	}
	.section-title em { font-style: italic; color: #7ec8e3; }

	/* ── Process ── */
	.process-steps { display: grid; grid-template-columns: repeat(4, 1fr); gap: 2rem; position: relative; }
	.process-line {
		position: absolute; top: 2rem; left: calc(12.5% + 0.75rem); right: calc(12.5% + 0.75rem);
		height: 1px; background: linear-gradient(90deg, transparent, rgba(168,216,240,0.35), transparent);
	}
	.process-step { text-align: center; padding-top: 0.5rem; }
	.step-num {
		display: inline-flex; align-items: center; justify-content: center;
		width: 4.25rem; height: 4.25rem; border-radius: 50%;
		border: 2px solid rgba(168,216,240,0.4);
		background: rgba(168,216,240,0.07);
		font-family: 'Playfair Display', Georgia, serif;
		font-size: 1.2rem; font-weight: 600; color: #a8d8f0; margin: 0 auto 1.5rem;
	}
	.step-title {
		font-family: 'Playfair Display', Georgia, serif;
		font-size: 1.15rem; font-weight: 700; color: #ffffff; margin-bottom: 0.75rem;
	}
	.step-desc { font-size: 0.85rem; color: rgba(190,210,228,0.82); line-height: 1.85; }

	/* ── Projekt-Slider ── */
	.proj-light { background: #f2f0ec; padding: 6rem 0; }
	.proj-light .section-header { margin-bottom: 3rem; }
	.proj-wide {
		max-width: 1420px; margin: 0 auto; padding: 0 2rem; position: relative;
	}
	.proj-stage { position: relative; display: flex; align-items: center; }
	.proj-clip {
		flex: 1; display: flex; gap: 1.25rem;
		overflow-x: auto; scroll-snap-type: x mandatory;
		-ms-overflow-style: none; scrollbar-width: none;
	}
	.proj-clip::-webkit-scrollbar { display: none; }
	.proj-slide {
		flex: 0 0 calc(33.33% - 0.84rem);
		scroll-snap-align: start;
		display: flex; flex-direction: column; gap: 0.75rem;
		transition: transform 0.35s cubic-bezier(0.16,1,0.3,1);
		background: none; border: none; padding: 0; cursor: pointer;
	}
	.proj-slide:hover { transform: translateY(-6px); }

	.proj-img-wrap { position: relative; overflow: hidden; border-radius: 8px; }
	.proj-img-wrap img { width: 100%; height: auto; object-fit: contain; display: block;
		box-shadow: 0 8px 32px rgba(14,14,22,0.12);
		transition: transform 0.5s cubic-bezier(0.16,1,0.3,1);
	}
	.proj-slide:hover .proj-img-wrap img { transform: scale(1.01); }
	.proj-footer {
		display: flex; align-items: center; justify-content: space-between;
		margin-top: 1.75rem; gap: 1rem;
	}
	.proj-dots { display: flex; gap: 6px; }
	.proj-dot {
		width: 6px; height: 6px; border-radius: 50%;
		background: rgba(14,14,22,0.2); border: none; cursor: pointer; padding: 0;
		transition: all 0.25s cubic-bezier(0.16,1,0.3,1);
	}
	.proj-dot.active { background: #0e0e16; width: 22px; border-radius: 3px; }
	.proj-counter {
		font-size: 0.65rem; font-weight: 500; letter-spacing: 0.12em;
		color: rgba(14,14,22,0.45); margin-right: auto; padding-left: 0.75rem;
	}
	.proj-eyebrow { color: rgba(14,14,22,0.4); }
	.proj-title { color: #0e0e16; }
	.proj-title em { color: #1a4a6b; }
	.proj-arrow {
		position: absolute; top: calc(50% - 1.75rem); z-index: 10;
		width: 44px; height: 44px; border-radius: 50%;
		background: #fff; backdrop-filter: blur(8px);
		border: 1px solid rgba(14,14,22,0.08);
		box-shadow: 0 2px 12px rgba(14,14,22,0.1), 0 8px 32px rgba(14,14,22,0.08);
		color: #0e0e16; cursor: pointer;
		display: flex; align-items: center; justify-content: center;
		transition: all 0.25s cubic-bezier(0.16,1,0.3,1);
		transform: translateY(-50%);
	}
	.proj-prev { left: -16px; }
	.proj-next { right: -16px; }
	.proj-arrow:hover {
		box-shadow: 0 4px 24px rgba(14,14,22,0.18), 0 12px 40px rgba(14,14,22,0.12);
		transform: translateY(-50%) scale(1.1);
	}
	.proj-link {
		font-size: 0.68rem; font-weight: 500; letter-spacing: 0.14em;
		text-transform: uppercase; color: rgba(14,14,22,0.55);
		border-bottom: 1px solid rgba(14,14,22,0.25);
		padding-bottom: 0.15rem; transition: all 0.2s; white-space: nowrap;
	}
	.proj-link:hover { color: #0e0e16; border-color: #0e0e16; }

	/* ── Lightbox ── */
	.lb-overlay {
		position: fixed; inset: 0; z-index: 500;
		background: rgba(4,4,12,0.94); backdrop-filter: blur(10px);
		display: flex; align-items: center; justify-content: center;
		animation: lbIn 0.2s ease both;
	}
	@keyframes lbIn { from { opacity: 0; } to { opacity: 1; } }
	.lb-img-wrap {
		max-width: 88vw; max-height: 84vh;
		display: flex; align-items: center; justify-content: center;
	}
	.lb-img-wrap img {
		max-width: 100%; max-height: 84vh;
		object-fit: contain; border-radius: 6px;
		box-shadow: 0 32px 80px rgba(0,0,0,0.7);
		animation: lbImgIn 0.25s cubic-bezier(0.16,1,0.3,1) both;
	}
	@keyframes lbImgIn { from { transform: scale(0.96); opacity: 0; } to { transform: scale(1); opacity: 1; } }
	.lb-close {
		position: absolute; top: 1.25rem; right: 1.25rem;
		width: 44px; height: 44px; border-radius: 50%;
		background: rgba(255,255,255,0.08); border: 1px solid rgba(255,255,255,0.15);
		color: rgba(255,255,255,0.75); font-size: 1.1rem; cursor: pointer;
		display: flex; align-items: center; justify-content: center; transition: all 0.2s;
	}
	.lb-close:hover { background: rgba(255,255,255,0.18); color: #fff; }
	.lb-nav {
		position: absolute; top: 50%; transform: translateY(-50%);
		width: 52px; height: 52px; border-radius: 50%;
		background: rgba(255,255,255,0.08); border: 1px solid rgba(255,255,255,0.15);
		color: rgba(255,255,255,0.8); cursor: pointer;
		display: flex; align-items: center; justify-content: center; transition: all 0.2s;
	}
	.lb-nav:hover { background: rgba(255,255,255,0.2); color: #fff; }
	.lb-prev { left: 1.25rem; }
	.lb-next { right: 1.25rem; }
	.lb-counter {
		position: absolute; bottom: 1.25rem; left: 50%; transform: translateX(-50%);
		font-size: 0.7rem; letter-spacing: 0.18em; text-transform: uppercase;
		color: rgba(255,255,255,0.4);
	}

	/* ── Footer ── */
	.home-footer { background: #04040c; }
	.top-line { height: 1px; background: rgba(168,216,240,0.1); }
	.footer-inner {
		display: flex; justify-content: space-between; align-items: flex-start;
		gap: 4rem; padding-top: 5rem; padding-bottom: 4rem; flex-wrap: wrap;
	}
	.brand .logo { display: inline-flex; flex: unset; margin-bottom: 1rem; }
	.brand .logo img { height: 52px; width: auto; display: block; }
	.brand p { font-size: 0.82rem; color: rgba(190,215,235,0.75); line-height: 1.7; }
	.cols { display: flex; gap: 4rem; }
	.col { display: flex; flex-direction: column; gap: 0.65rem; }
	.col h4 {
		font-size: 0.62rem; font-weight: 600; letter-spacing: 0.2em;
		text-transform: uppercase; color: rgba(255,255,255,0.7); margin-bottom: 0.5rem;
	}
	.col a, .col span {
		font-size: 0.82rem; color: rgba(255,255,255,0.62); transition: color 0.2s;
		word-break: break-word; overflow-wrap: break-word;
	}
	.col a:hover { color: #fff; }
	.bottom { border-top: 1px solid rgba(255,255,255,0.1); }
	.bottom-inner {
		display: flex; align-items: center; justify-content: space-between;
		padding: 1.5rem 0; font-size: 0.72rem; color: rgba(255,255,255,0.52);
	}
	.legal { display: flex; gap: 1.5rem; }
	.legal a { color: rgba(255,255,255,0.52); transition: color 0.2s; }
	.legal a:hover { color: rgba(168,216,240,0.85); }

	/* ── Responsive ── */
	@media (max-width: 900px) {
		.process-steps { grid-template-columns: 1fr 1fr; }
		.process-line { display: none; }
		.hero-inner { flex-direction: column; gap: 2rem; }
		.hero-stats { align-self: auto; text-align: left; padding-bottom: 2rem; flex-direction: row; gap: 0; justify-content: space-between; width: 100%; }
		.hstat { padding: 0 1.5rem 0 0; }
		.hstat-sep { width: 1px; height: auto; }
		.philo-cards { grid-template-columns: 1fr; gap: 1rem; }
		.philosophy { padding: 5rem 0; }
		.footer-inner { gap: 2.5rem; }
		.cols { gap: 2rem; }
	}
	@media (max-width: 768px) {
		.burger { display: flex; }
		.links {
			display: none; position: fixed; top: 0; right: 0; bottom: 0;
			width: min(300px, 85vw); background: #0a0a0f;
			border-left: 1px solid rgba(168,216,240,0.08);
			flex-direction: column; align-items: flex-start;
			padding: 6rem 2rem 2rem; gap: 0.25rem; z-index: 295;
		}
		.links.open { display: flex; }
		.links a:not(.nav-cta) {
			padding: 0.875rem 0; font-size: 0.85rem; width: 100%;
			border-bottom: 1px solid rgba(168,216,240,0.05);
		}
		.nav-cta { margin-left: 0; margin-top: 1.5rem; }
		.logo { font-size: 1.6rem; }
		.hero-inner { padding-top: 4.5rem; flex-direction: column; gap: 1.5rem; }
		.hero-left  { padding-bottom: 0; }
		.hero-stats { align-self: auto; text-align: left; padding-bottom: 2.5rem; flex-direction: row; gap: 0; justify-content: space-between; width: 100%; }
		.hstat { padding: 0 1.25rem 0 0; }
		.hstat-sep { width: 1px; height: auto; background: rgba(168,216,240,0.06); }

		.hero-watermark { font-size: 22rem; right: -15%; bottom: -5%; }
		.process-steps { grid-template-columns: 1fr; }
		.footer-inner { flex-direction: column; gap: 3rem; padding: 4rem 0 3rem; }
		.cols { gap: 2.5rem; }
		.bottom-inner { flex-direction: column; gap: 1rem; text-align: center; }
		.section { padding: 6rem 0; }
		.philo-cards { grid-template-columns: 1fr; gap: 1rem; }
		.philosophy { padding: 5rem 0; }
		.philo-lead-sub { white-space: normal; }
	}
	@media (max-width: 480px) {
		.hero-actions { flex-direction: column; }
		.hero-actions a { text-align: center; justify-content: center; }
		.hero-watermark { font-size: 14rem; right: -10%; bottom: -2%; }
		.proj-slide { flex: 0 0 82%; }
		.hstat-num { font-size: 1.4rem; }
		.hstat-label { font-size: 0.58rem; }
	}
</style>
