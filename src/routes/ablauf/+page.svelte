<script>
	import { reveal } from '$lib/actions.js';

	const steps = [
		{ num: '01', label: 'ca. 15 Min.',  title: 'Kostenloses Erstgespräch',
		  desc: 'Wir lernen Ihr Unternehmen kennen. Sie erzählen uns was Sie sich wünschen und wir erklären was möglich ist. Kein Verkaufsgespräch, sondern echte Beratung.' },
		{ num: '02', label: '3–5 Tage',         title: 'Offerte & Konzept',
		  desc: 'Sie erhalten eine klare Offerte mit Festpreis. Keine versteckten Kosten.' },
		{ num: '03', label: '3–5 Wochen',        title: 'Design & Entwicklung',
		  desc: 'Wir bauen Ihre Website mit regelmässigen Updates und einer Vorschau vor dem Launch. Wir passen an bis alles stimmt.' },
		{ num: '04', label: 'Fertig!',            title: 'Launch & Übergabe',
		  desc: 'Ihre Website geht online. 3 Monate lang bleiben wir als Ansprechpartner erreichbar.' }
	];

	const faqs = [
		{ q: 'Was brauche ich, um zu starten?',
		  a: 'Eigentlich nur eine Idee. Texte, Bilder und Inhalte erarbeiten wir gemeinsam – Sie müssen kein IT-Experte sein.' },
		{ q: 'Kostet das Erstgespräch etwas?',
		  a: 'Nein. Das erste Gespräch ist vollständig kostenlos und unverbindlich. Erst wenn Sie sich für uns entscheiden, beginnt das Projekt.' },
		{ q: 'Kann ich die Website später selbst bearbeiten?',
		  a: 'Ja. Wir bauen sie so, dass Sie einfache Änderungen wie Texte oder Öffnungszeiten selbst anpassen können.' },
		{ q: 'Was passiert nach der Übergabe?',
		  a: 'Die ersten 3 Monate sind im Preis inbegriffen. Danach bieten wir optionale Wartungspakete an – oder Sie betreuen die Website selbst.' },
		{ q: 'Wie läuft die Zusammenarbeit ab?',
		  a: 'Per E-Mail, Telefon oder Video-Call – ganz wie Sie möchten. Kein Bürobesuch nötig, keine komplizierte Software.' }
	];

	let open = $state(-1);
</script>

<svelte:head>
	<title>Unser Ablauf – Aura</title>
</svelte:head>

<div class="page-hero">
	<div class="container">
		<span class="eyebrow">Wie wir arbeiten</span>
		<h1>Unser Ablauf</h1>
		<p style="white-space: nowrap;">Klar, transparent und von Anfang bis Schluss für Sie da.</p>
	</div>
</div>

<section class="section section-black">
	<div class="container steps-wrap">
		{#each steps as s, i}
			<div class="step" use:reveal={{ delay: i * 60 }}>
				<div class="step-aside">
					<div class="step-circle">{s.num}</div>
					{#if i < steps.length - 1}
						<div class="step-connector"></div>
					{/if}
				</div>
				<div class="step-body">
					<span class="step-label">{s.label}</span>
					<h2 class="step-title display">{s.title}</h2>
					<p>{s.desc}</p>
				</div>
			</div>
		{/each}
	</div>
</section>

<section class="section section-dark">
	<div class="container faq-wrap">
		<div use:reveal>
			<span class="eyebrow">FAQ</span>
			<h2 class="display faq-title">Häufige Fragen</h2>
		</div>
		<div class="faq-list" use:reveal={{ delay: 100 }}>
			{#each faqs as f, i}
				<div class="faq-item" class:open={open === i}>
					<button onclick={() => open = open === i ? -1 : i}>
						<span>{f.q}</span>
						<span class="faq-icon">{open === i ? '−' : '+'}</span>
					</button>
					{#if open === i}
						<p class="faq-ans">{f.a}</p>
					{/if}
				</div>
			{/each}
		</div>
	</div>
</section>

<div class="cta-block">
	<div class="container" use:reveal>
		<span class="eyebrow">Erster Schritt</span>
		<h2>Bereit für das<br /><em>erste Gespräch?</em></h2>
		<p>Kostenlos, unverbindlich, 15 Minuten.</p>
		<div class="cta-actions">
			<a href="/kontakt" class="btn-border">Termin anfragen</a>
		</div>
	</div>
</div>

<style>
	.steps-wrap { max-width: 700px; margin: 0 auto; position: relative; }
	.step { display: flex; gap: 2.5rem; }
	.step-aside { display: flex; flex-direction: column; align-items: center; flex-shrink: 0; }
	.step-circle {
		width: 56px; height: 56px;
		border: 1.5px solid rgba(168,216,240,0.6);
		color: var(--ice);
		border-radius: 50%;
		display: flex; align-items: center; justify-content: center;
		font-family: 'Playfair Display', Georgia, serif;
		font-size: 1rem; font-weight: 600;
		letter-spacing: 0.04em;
		flex-shrink: 0;
		background: #0a0a0f;
		position: relative; z-index: 1;
		box-shadow: 0 0 16px rgba(168,216,240,0.2), 0 0 36px rgba(168,216,240,0.07);
	}
	.step-connector {
		width: 1px; flex: 1;
		background: rgba(168,216,240,0.08);
		margin: 0.75rem 0;
		min-height: 2rem;
		position: relative;
		overflow: hidden;
	}
	.step-connector::after {
		content: '';
		position: absolute;
		top: -80%; left: 0;
		width: 100%; height: 80%;
		background: linear-gradient(to bottom, transparent, rgba(200,235,255,1), transparent);
		animation: flow-single 6s linear infinite;
		opacity: 0;
	}
	.step:nth-child(1) .step-connector::after { animation-delay: 0s; }
	.step:nth-child(2) .step-connector::after { animation-delay: 2s; }
	.step:nth-child(3) .step-connector::after { animation-delay: 4s; }

	@keyframes flow-single {
		0%        { top: -80%; opacity: 1; }
		38%       { top: 160%; opacity: 1; }
		39%, 100% { top: -80%; opacity: 0; }
	}
	.step-body { padding-bottom: 4rem; padding-top: 0.5rem; }
	.step-label {
		font-size: 0.62rem;
		font-weight: 600;
		letter-spacing: 0.2em;
		text-transform: uppercase;
		color: var(--ice);
		display: block;
		margin-bottom: 0.75rem;
	}
	.step-title { font-size: 1.6rem; font-weight: 700; color: #ffffff; margin-bottom: 1rem; letter-spacing: -0.01em; }
	.step-body p { font-size: 0.92rem; color: rgba(195,210,225,0.82); line-height: 1.9; }

	.faq-wrap { max-width: 660px; margin: 0 auto; }
	.faq-title { font-size: clamp(2rem, 4vw, 2.75rem); color: #fff; margin: 0.25rem 0 2.5rem; }
	.faq-list { display: flex; flex-direction: column; }
	.faq-item { border-bottom: 1px solid rgba(255,255,255,0.06); }
	.faq-item:first-child { border-top: 1px solid rgba(255,255,255,0.06); }
	.faq-item button {
		width: 100%;
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 1.5rem 0;
		background: none; border: none; cursor: pointer;
		text-align: left;
		font-size: 0.9rem; font-weight: 400;
		color: rgba(255,255,255,0.65);
		gap: 1rem;
		transition: color 0.2s;
	}
	.faq-item.open button, .faq-item button:hover { color: #fff; }
	.faq-icon { color: var(--ice); font-size: 1.1rem; flex-shrink: 0; }
	.faq-ans {
		font-size: 0.875rem; color: rgba(195,210,225,0.78);
		line-height: 1.8;
		padding-bottom: 1.5rem;
	}

	@media (max-width: 768px) {
		.step { gap: 1.5rem; }
		.step-circle { width: 44px; height: 44px; font-size: 0.85rem; }
		.step-title { font-size: 1.3rem; }
		.step-body { padding-bottom: 2.5rem; }
	}
	@media (max-width: 480px) {
		.step { gap: 1rem; }
		.step-circle { width: 38px; height: 38px; font-size: 0.78rem; }
		.step-title { font-size: 1.15rem; }
		.step-body { padding-bottom: 2rem; }
		.faq-item button { font-size: 0.85rem; padding: 1.25rem 0; }
	}
</style>
