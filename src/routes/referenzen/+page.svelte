<script>
	import { reveal } from '$lib/actions.js';

	const projects = [
		{ src: '/projekte/ref-3.png', name: 'LivingSpaces',       cat: 'Immobilien & Liegenschaften' },
		{ src: '/projekte/ref-1.png', name: 'Hausarztpraxis',    cat: 'Medizin & Familie' },
		{ src: '/projekte/ref-2.png', name: 'Pure Balance',       cat: 'Beauty & Wellness' },
		{ src: '/projekte/ref-4.png', name: 'Naturheilpraxis',    cat: 'Gesundheit & Therapie' },
	];

	let lbOpen = $state(false);
	let lbIdx  = $state(0);

	function openLb(i) { lbIdx = i; lbOpen = true; }
	function closeLb() { lbOpen = false; }
	function lbNext()  { lbIdx = (lbIdx + 1) % projects.length; }
	function lbPrev()  { lbIdx = (lbIdx - 1 + projects.length) % projects.length; }

	$effect(() => {
		if (!lbOpen) return;
		const fn = (e) => {
			if (e.key === 'Escape')     closeLb();
			if (e.key === 'ArrowRight') lbNext();
			if (e.key === 'ArrowLeft')  lbPrev();
		};
		window.addEventListener('keydown', fn);
		return () => window.removeEventListener('keydown', fn);
	});
</script>

<svelte:head>
	<title>Projekte – Aura</title>
	<link rel="preconnect" href="https://fonts.googleapis.com" />
	<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@1,300;1,400&family=Montserrat:wght@400;500&display=swap" rel="stylesheet" />
</svelte:head>

<!-- ══ HERO ══ -->
<section class="ref-hero">
	<div class="container">
		<div class="rh-inner" use:reveal>
			<span class="eyebrow">Unsere Arbeiten</span>
			<h1 class="rh-title">Projekte, die<br /><em>für sich sprechen.</em></h1>
			<p class="rh-sub">So könnte Ihre Website aussehen.</p>
		</div>
	</div>
</section>

<!-- ══ SHOWCASE GRID ══ -->
<div class="showcase">
	<div class="sc-grid">
		{#each projects as p, i}
			<button class="sc-item sc-{i + 1}" onclick={() => openLb(i)}>
				<img src={p.src} alt={p.name} loading="lazy" />
				<div class="sc-overlay">
					<div class="sc-info">
						<span class="sc-name">{p.name}</span>
						<span class="sc-cat">{p.cat}</span>
					</div>
				</div>
			</button>
		{/each}
	</div>
</div>

<!-- ══ LIGHTBOX ══ -->
{#if lbOpen}
<div class="lb-overlay" onclick={closeLb} role="dialog" aria-modal="true">
	<button class="lb-close" onclick={closeLb} aria-label="Schliessen">✕</button>
	<button class="lb-nav lb-prev" onclick={(e) => { e.stopPropagation(); lbPrev(); }} aria-label="Vorheriges">
		<svg width="18" height="18" viewBox="0 0 16 16" fill="none"><path d="M10 12L6 8l4-4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></svg>
	</button>
	<div class="lb-img-wrap" onclick={(e) => e.stopPropagation()}>
		<img src={projects[lbIdx].src} alt={projects[lbIdx].name} />
	</div>
	<button class="lb-nav lb-next" onclick={(e) => { e.stopPropagation(); lbNext(); }} aria-label="Nächstes">
		<svg width="18" height="18" viewBox="0 0 16 16" fill="none"><path d="M6 4l4 4-4 4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></svg>
	</button>
	<span class="lb-counter">{lbIdx + 1} / {projects.length}</span>
</div>
{/if}

<!-- ══ CTA ══ -->
<div class="cta-block">
	<div class="container" use:reveal>
		<span class="eyebrow">Nächstes Projekt</span>
		<h2>Ihr Unternehmen<br /><em>fehlt noch hier.</em></h2>
		<p>Lassen Sie uns gemeinsam etwas aufbauen, das Ihre Kunden begeistert.</p>
		<div class="cta-actions">
			<a href="/kontakt" class="btn-border">Projekt besprechen</a>
		</div>
	</div>
</div>

<style>
	/* ── HERO ── */
	.ref-hero {
		padding: 10rem 0 5.5rem;
		background: var(--bg0);
		border-bottom: 1px solid var(--border);
	}
	.rh-inner { max-width: 680px; }
	.rh-title {
		font-family: var(--font-h);
		font-size: clamp(2.8rem, 6vw, 5rem);
		font-weight: 600; color: var(--text);
		letter-spacing: -0.025em; line-height: 1.1;
		margin: 0.75rem 0 1.5rem;
	}
	.rh-title em { font-style: italic; color: var(--ice); }
	.rh-sub { color: var(--muted); font-size: 1.05rem; line-height: 1.8; }

	/* ── SHOWCASE GRID ── */
	.showcase { background: #060608; }
	.sc-grid {
		display: grid;
		grid-template-columns: 1fr 1fr;
		gap: 3px;
		align-items: start;
	}


	.sc-item {
		position: relative;
		border: none; padding: 2.5rem; cursor: pointer;
		display: block; background: #0d0d14;
		width: 100%;
	}
	.sc-item img {
		width: 100%;
		height: auto;
		display: block;
		transition: transform 0.5s ease;
	}
	.sc-item:hover img { transform: scale(1.02); }
	.sc-item:active { background: #0d0d14; }
	.sc-item:focus { outline: none; }

	/* Overlay — nur unterer Gradient */
	.sc-overlay {
		position: absolute; inset: 0;
		display: flex; align-items: flex-end;
		background: linear-gradient(to top, rgba(4,4,12,0) 0%, transparent 100%);
		transition: background 0.4s ease;
	}
	.sc-item:hover .sc-overlay {
		background: linear-gradient(to top, rgba(4,4,12,0.92) 0%, rgba(4,4,12,0.4) 50%, transparent 75%);
	}

	.sc-info {
		display: flex; flex-direction: column; gap: 0.5rem;
		padding: 1.75rem 1.75rem;
		opacity: 0; transform: translateY(10px);
		transition: opacity 0.4s ease, transform 0.4s ease;
		pointer-events: none;
	}
	.sc-item:hover .sc-info { opacity: 1; transform: translateY(0); }

	.sc-name {
		font-family: 'Cormorant Garamond', 'Playfair Display', Georgia, serif;
		font-style: italic; font-weight: 400;
		font-size: 2.4rem; color: #fff; line-height: 1.1;
	}
	.sc-cat {
		font-family: 'Montserrat', 'DM Sans', sans-serif;
		font-size: 0.68rem; font-weight: 500;
		letter-spacing: 0.26em; text-transform: uppercase;
		color: var(--ice);
	}

	/* ── LIGHTBOX ── */
	.lb-overlay {
		position: fixed; inset: 0; z-index: 1000;
		background: rgba(4, 4, 12, 0.97);
		display: flex; align-items: center; justify-content: center;
		backdrop-filter: blur(8px);
	}
	.lb-img-wrap {
		max-width: 92vw; max-height: 88vh;
		display: flex; align-items: center; justify-content: center;
	}
	.lb-img-wrap img {
		max-width: 100%; max-height: 88vh;
		object-fit: contain; display: block;
		box-shadow: 0 40px 120px rgba(0, 0, 0, 0.9);
	}
	.lb-close {
		position: fixed; top: 1.5rem; right: 1.75rem;
		background: none; border: none; cursor: pointer;
		color: rgba(255,255,255,0.55); font-size: 1.5rem;
		transition: color 0.2s; z-index: 1001; line-height: 1;
	}
	.lb-close:hover { color: #fff; }
	.lb-nav {
		position: fixed; top: 50%; transform: translateY(-50%);
		background: rgba(255,255,255,0.07); border: 1px solid rgba(255,255,255,0.12);
		color: #fff; cursor: pointer; width: 50px; height: 50px;
		display: flex; align-items: center; justify-content: center;
		border-radius: 2px; transition: background 0.2s; z-index: 1001;
	}
	.lb-nav:hover { background: rgba(255,255,255,0.16); }
	.lb-prev { left: 1.5rem; }
	.lb-next { right: 1.5rem; }
	.lb-counter {
		position: fixed; bottom: 1.75rem; left: 50%; transform: translateX(-50%);
		font-size: 0.7rem; letter-spacing: 0.2em; color: rgba(255,255,255,0.4);
		z-index: 1001;
	}

	/* ── RESPONSIVE ── */
	@media (max-width: 768px) {
		.ref-hero { padding: 8rem 0 4rem; }
		.sc-grid { grid-template-columns: 1fr; }
		.sc-item { padding: 1.5rem; }
		.sc-name { font-size: 1.6rem; }
	}
	@media (max-width: 480px) {
		.sc-item { padding: 0.5rem; }
	}
</style>
