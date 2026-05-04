<script>
	import { reveal } from '$lib/actions.js';

	const services = [
		{
			num: '01',
			title: 'Website-Erstellung',
			desc: 'Sie haben noch keine Website oder möchten ganz von vorne beginnen? Wir erstellen Ihnen eine professionelle, moderne Website. Von der ersten Idee bis zum fertigen Ergebnis.',
			includes: [
				'Individuelles Design nach Ihren Wünschen',
				'Mobil optimiert für alle Geräte',
				'Professionelle Texte inklusive',
				'Kontaktformular',
				'Schnelle Ladezeiten',
				'Auf Ihre Marke zugeschnitten',
				'3 Monate Support nach Launch'
			]
		},
		{
			num: '02',
			title: 'Website-Überarbeitung',
			desc: 'Ihre Website ist in die Jahre gekommen? Wir modernisieren sie von Grund auf. Neues Design, bessere Technik, aktueller Inhalt. Nahtlos und ohne Ausfallzeit.',
			includes: [
				'Analyse der bestehenden Website',
				'Neues, zeitgemässes Design',
				'Überarbeitung und Optimierung der Texte',
				'Technische Modernisierung',
				'Bestehende Inhalte übernehmen',
				'Nahtloser Übergang'
			]
		},
	];

</script>

<svelte:head>
	<title>Leistungen – Aura</title>
</svelte:head>

<div class="page-hero">
	<div class="container">
		<span class="eyebrow">Was wir tun</span>
		<h1>Unsere Leistungen</h1>
		<p>Alles rund um Ihre Website. Aus einer Hand, mit persönlicher Betreuung.</p>
	</div>
</div>

<section class="section section-black srv-section">
	<div class="container">
		{#each services as s, i}
			<div class="srv-block" use:reveal>
				<div class="srv-intro">
					<span class="srv-num">{s.num}</span>
					<h2 class="display">{s.title}</h2>
					<p class="srv-desc">{s.desc}</p>
					<a href="/kontakt" class="btn-border" style="margin-top:2rem">Anfrage stellen</a>
				</div>
				<div class="srv-includes" use:reveal={{ delay: 120 }}>
					<p class="includes-label">Inklusive</p>
					<ul>
						{#each s.includes as item}
							<li>
								<span class="dot">—</span>
								{item}
							</li>
						{/each}
					</ul>
				</div>
			</div>
			{#if i < services.length - 1}
				<div class="line-divider" style="margin: 1rem 0"></div>
			{/if}
		{/each}
	</div>
</section>


<style>
	.srv-section { padding-top: 1rem; }
	.srv-num {
		display: inline-block;
		font-family: 'Playfair Display', Georgia, serif;
		font-size: clamp(1.6rem, 3vw, 2.2rem);
		font-weight: 400;
		color: var(--ice);
		letter-spacing: -0.02em;
		line-height: 1;
		margin-bottom: 0.75rem;
	}
	:global(.page-hero p) { max-width: none; white-space: nowrap; }

	.srv-block {
		display: grid;
		grid-template-columns: 1fr 1fr;
		gap: 5rem;
		align-items: start;
		padding: 5rem 0;
	}
	.srv-intro h2 {
		font-size: clamp(1.75rem, 3.5vw, 2.75rem);
		font-weight: 600;
		color: #fff;
		margin: 0.5rem 0 1.25rem;
	}
	.srv-desc { font-size: 0.95rem; color: rgba(200,218,235,0.85); line-height: 1.85; }

	.includes-label {
		font-size: 0.82rem;
		font-weight: 500;
		letter-spacing: 0.2em;
		text-transform: uppercase;
		color: var(--ice);
		margin-bottom: 1.5rem;
	}
	.srv-includes ul { list-style: none; display: flex; flex-direction: column; gap: 1rem; }
	.srv-includes li { display: flex; gap: 1rem; font-size: 0.9rem; color: rgba(230,242,252,0.95); }
	.dot { color: var(--ice); flex-shrink: 0; }

	@media (max-width: 768px) {
		.srv-block { grid-template-columns: 1fr; gap: 3rem; padding: 4rem 0; }
		:global(.page-hero p) { white-space: normal; }
	}
	@media (max-width: 480px) {
		.srv-block { gap: 2rem; padding: 3rem 0; }
		.srv-intro h2 { font-size: 1.5rem; }
	}
</style>
