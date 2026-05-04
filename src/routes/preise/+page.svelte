<script>
	import { reveal } from '$lib/actions.js';

	const plans = [
		{
			name: 'Starter',
			price: '890',
			tagline: 'Der perfekte Einstieg',
			desc: 'Ideal für Unternehmen, die erstmals online gehen möchten – schnell, professionell und ohne Kompromisse.',
			features: [
				'Individuelles Design nach Ihren Wünschen',
				'Mobil optimiert für alle Geräte',
				'Professionelle Texte inklusive',
				'Kontaktformular',
				'Schnelle Ladezeiten',
				'Auf Ihre Marke zugeschnitten',
				'3 Monate Support nach Launch',
			],
			cta: 'Jetzt starten',
			popular: false,
		},
		{
			name: 'Professional',
			price: '1\'490',
			tagline: 'Unsere beliebteste Lösung',
			desc: 'Für Unternehmen, die mehr wollen: mehr Seiten, mehr Funktionen, mehr Wirkung – zu einem fairen Festpreis.',
			features: [
				'Bis zu 7 Seiten',
				'Premium-Design',
				'Mobil-optimiert',
				'Kontaktformular & Google Maps',
				'SEO-Optimierung',
				'Bildergalerie oder Blog',
				'3 Monate Support nach Launch',
			],
			cta: 'Projekt starten',
			popular: true,
		},
		{
			name: 'Premium',
			price: '2\'490',
			tagline: 'Die Komplettlösung',
			desc: 'Für anspruchsvolle Unternehmen, die das Beste wollen – massgeschneidert, umfassend und mit Priorität-Betreuung.',
			features: [
				'Bis zu 12 Seiten',
				'Massgeschneidertes Premium-Design',
				'Mobil-optimiert',
				'Alle Formulare & Integrationen',
				'Erweiterte SEO-Optimierung',
				'Mehrsprachigkeit möglich',
				'Animations & Interaktionen',
				'6 Monate Support nach Launch',
			],
			cta: 'Angebot anfragen',
			popular: false,
		},
	];

	const faqs = [
		{
			q: 'Sind das Festpreise?',
			a: 'Ja. Sie erhalten vor Projektbeginn eine klare Offerte mit dem genauen Preis – keine versteckten Kosten, keine Überraschungen.',
		},
		{
			q: 'Was ist nach dem Support-Zeitraum?',
			a: 'Nach dem inkludierten Support können Sie jederzeit ein optionales Wartungspaket dazubuchen – oder die Website selbst pflegen.',
		},
		{
			q: 'Kann ich zwischen den Paketen wechseln?',
			a: 'Selbstverständlich. Wenn Sie sich nicht sicher sind, welches Paket passt, beraten wir Sie gerne in einem kostenlosen Erstgespräch.',
		},
		{
			q: 'Sind Hosting und Domain inbegriffen?',
			a: 'Hosting und Domain sind nicht im Paketpreis enthalten, wir helfen Ihnen aber gerne bei der Auswahl und Einrichtung – zum besten Preis.',
		},
		{
			q: 'Was wenn ich mehr Seiten brauche?',
			a: 'Zusätzliche Seiten können jederzeit ergänzt werden. Die genauen Konditionen besprechen wir individuell.',
		},
	];

	let openFaq = $state(-1);
</script>

<svelte:head>
	<title>Preise – Aura</title>
</svelte:head>

<div class="page-hero">
	<div class="container">
		<span class="eyebrow">Transparente Preise</span>
		<h1>Preise</h1>
		<p>Festpreise ohne Überraschungen. Wählen Sie das Paket, das zu Ihrem Unternehmen passt.</p>
	</div>
</div>

<section class="section section-black pricing-section">
	<div class="container">
		<div class="plans-grid">
			{#each plans as plan, i}
				<div class="plan-card" class:plan-popular={plan.popular} use:reveal={{ delay: i * 80 }}>
					{#if plan.popular}
						<div class="popular-badge">Empfohlen</div>
					{/if}
					<div class="plan-head">
						<span class="plan-name">{plan.name}</span>
						<div class="plan-price">
							<span class="price-currency">CHF</span>
							<span class="price-amount">{plan.price}</span>
						</div>
						<p class="plan-tagline">{plan.tagline}</p>
					</div>
					<p class="plan-desc">{plan.desc}</p>
					<ul class="plan-features">
						{#each plan.features as feat}
							<li>
								<span class="feat-check">✓</span>
								{feat}
							</li>
						{/each}
					</ul>
					<a href="/kontakt" class={plan.popular ? 'plan-cta plan-cta-primary' : 'plan-cta'}>{plan.cta} →</a>
				</div>
			{/each}
		</div>

		<p class="price-note" use:reveal={{ delay: 200 }}>
			Alle Preise in CHF, exkl. MwSt. · Festpreisgarantie · Kostenlose Erstberatung
		</p>
	</div>
</section>

<section class="section section-dark faq-section">
	<div class="container">
		<div class="faq-header" use:reveal>
			<span class="eyebrow">Häufige Fragen</span>
			<h2 class="display section-title">Was Sie wissen<br /><em>möchten.</em></h2>
		</div>
		<div class="faq-list">
			{#each faqs as faq, i}
				<div class="faq-item" use:reveal={{ delay: i * 50 }}>
					<button
						class="faq-q"
						class:faq-open={openFaq === i}
						onclick={() => openFaq = openFaq === i ? -1 : i}
					>
						<span>{faq.q}</span>
						<span class="faq-icon">{openFaq === i ? '−' : '+'}</span>
					</button>
					{#if openFaq === i}
						<p class="faq-a">{faq.a}</p>
					{/if}
				</div>
			{/each}
		</div>
	</div>
</section>

<div class="cta-block">
	<div class="container" use:reveal>
		<span class="eyebrow">Unverbindlich starten</span>
		<h2>Nicht sicher,<br /><em>welches Paket passt?</em></h2>
		<p>Erstes Gespräch kostenlos. Wir beraten Sie ehrlich – ohne Druck.</p>
		<div class="cta-actions">
			<a href="/kontakt" class="btn-border">Kostenlos beraten lassen</a>
		</div>
	</div>
</div>

<style>
	/* ── Pricing Grid ── */
	.pricing-section { padding-top: 5rem; }

	.plans-grid {
		display: grid;
		grid-template-columns: repeat(3, 1fr);
		gap: 1.5rem;
		margin-bottom: 2.5rem;
	}

	.plan-card {
		background: #0a0a0f;
		border: 1px solid rgba(168,216,240,0.08);
		padding: 2.75rem 2.25rem;
		display: flex;
		flex-direction: column;
		gap: 1.75rem;
		position: relative;
		transition: border-color 0.3s;
	}
	.plan-card:hover {
		border-color: rgba(168,216,240,0.2);
	}

	.plan-popular {
		border-color: rgba(201,169,110,0.35);
		background: linear-gradient(160deg, rgba(201,169,110,0.04) 0%, #0a0a0f 60%);
	}
	.plan-popular:hover {
		border-color: rgba(201,169,110,0.55);
	}

	.popular-badge {
		position: absolute;
		top: -1px; left: 50%;
		transform: translateX(-50%);
		background: #c9a96e;
		color: #06060a;
		font-size: 0.6rem;
		font-weight: 700;
		letter-spacing: 0.2em;
		text-transform: uppercase;
		padding: 0.3rem 1.25rem;
	}

	.plan-head { display: flex; flex-direction: column; gap: 0.75rem; }

	.plan-name {
		font-size: 0.65rem;
		font-weight: 600;
		letter-spacing: 0.22em;
		text-transform: uppercase;
		color: rgba(255,255,255,0.55);
	}
	.plan-popular .plan-name { color: #c9a96e; }

	.plan-price {
		display: flex;
		align-items: baseline;
		gap: 0.4rem;
	}
	.price-currency {
		font-size: 1rem;
		font-weight: 500;
		color: rgba(255,255,255,0.5);
		letter-spacing: 0.05em;
	}
	.price-amount {
		font-family: 'Playfair Display', Georgia, serif;
		font-size: clamp(2.5rem, 4vw, 3.25rem);
		font-weight: 700;
		color: #f0f4f8;
		letter-spacing: -0.03em;
		line-height: 1;
	}
	.plan-popular .price-amount { color: #c9a96e; }

	.plan-tagline {
		font-size: 0.78rem;
		color: rgba(255,255,255,0.42);
		letter-spacing: 0.02em;
	}

	.plan-desc {
		font-size: 0.875rem;
		color: rgba(195,212,228,0.75);
		line-height: 1.85;
		padding-bottom: 1.25rem;
		border-bottom: 1px solid rgba(255,255,255,0.06);
	}

	.plan-features {
		list-style: none;
		display: flex;
		flex-direction: column;
		gap: 0.75rem;
		flex: 1;
	}
	.plan-features li {
		display: flex;
		align-items: flex-start;
		gap: 0.75rem;
		font-size: 0.85rem;
		color: rgba(220,235,248,0.82);
		line-height: 1.5;
	}
	.feat-check {
		color: #a8d8f0;
		font-size: 0.7rem;
		font-weight: 700;
		flex-shrink: 0;
		margin-top: 0.2rem;
	}
	.plan-popular .feat-check { color: #c9a96e; }

	.plan-cta {
		display: inline-flex;
		align-items: center;
		justify-content: center;
		padding: 0.9rem 1.5rem;
		border: 1px solid rgba(168,216,240,0.25);
		color: rgba(168,216,240,0.75);
		font-size: 0.68rem;
		font-weight: 500;
		letter-spacing: 0.14em;
		text-transform: uppercase;
		transition: all 0.3s;
		border-radius: 2px;
		margin-top: auto;
	}
	.plan-cta:hover {
		background: rgba(168,216,240,0.08);
		border-color: #a8d8f0;
		color: #a8d8f0;
	}

	.plan-cta-primary {
		background: rgba(201,169,110,0.1);
		border-color: rgba(201,169,110,0.45);
		color: #c9a96e;
	}
	.plan-cta-primary:hover {
		background: rgba(201,169,110,0.18);
		border-color: #c9a96e;
		color: #d4b07c;
	}

	.price-note {
		text-align: center;
		font-size: 0.75rem;
		color: rgba(255,255,255,0.35);
		letter-spacing: 0.06em;
	}

	/* ── FAQ ── */
	.faq-section { padding-bottom: 8rem; }

	.faq-header { margin-bottom: 3.5rem; }
	.faq-header .section-title { margin-top: 0.5rem; }

	.faq-list {
		max-width: 720px;
		display: flex;
		flex-direction: column;
		border-top: 1px solid rgba(255,255,255,0.06);
	}

	.faq-item { border-bottom: 1px solid rgba(255,255,255,0.06); }

	.faq-q {
		width: 100%;
		display: flex;
		align-items: center;
		justify-content: space-between;
		gap: 1.5rem;
		padding: 1.5rem 0;
		background: none;
		border: none;
		cursor: pointer;
		text-align: left;
		font-size: 0.95rem;
		font-weight: 400;
		color: rgba(240,244,248,0.88);
		transition: color 0.2s;
	}
	.faq-q:hover { color: #f0f4f8; }
	.faq-open { color: #f0f4f8; }

	.faq-icon {
		font-size: 1.25rem;
		color: rgba(168,216,240,0.5);
		flex-shrink: 0;
		font-weight: 300;
		line-height: 1;
	}

	.faq-a {
		font-size: 0.9rem;
		color: rgba(195,210,225,0.75);
		line-height: 1.85;
		padding-bottom: 1.5rem;
		max-width: 600px;
	}

	@media (max-width: 960px) {
		.plans-grid { grid-template-columns: 1fr; max-width: 480px; }
	}
	@media (max-width: 600px) {
		.plans-grid { max-width: 100%; }
		.plan-card { padding: 2.25rem 1.75rem; }
	}
</style>
