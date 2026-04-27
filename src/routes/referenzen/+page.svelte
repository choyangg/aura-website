<script>
	import { reveal } from '$lib/actions.js';

	const projects = [
		{ src: '/projekte/livingspaces.png', cat: 'Immobilien',        title: 'LivingSpaces',  desc: 'Moderne Immobilien-Website mit Objektübersicht, Galerie und direktem Kontaktformular.' },
		{ src: '/projekte/beauty.png',       cat: 'Beauty & Kosmetik', title: 'Lumina Studio', desc: 'Elegante Website mit Behandlungsübersicht, Preisliste und Online-Terminanfrage.' },
		{ src: '/projekte/Holzwerk.png',     cat: 'Schreinerei',       title: 'Holzwerk',      desc: 'Handwerklicher Auftritt mit Referenzgalerie, Leistungen und Kontaktformular.' },
		{ src: '/projekte/Oasis.png',        cat: 'Restaurant & Bar',  title: 'Oasis',         desc: 'Atmosphärische Website mit Speisekarte, Öffnungszeiten und Tischreservierung.' },
		{ src: '/projekte/Physiovita.png',   cat: 'Physiotherapie',    title: 'PhysioVita',    desc: 'Vertrauensvoller Praxis-Auftritt mit Leistungen, Team-Vorstellung und Kontakt.' },
		{ src: '/projekte/Zahnwerk.png',     cat: 'Zahnarztpraxis',    title: 'Zahnwerk',      desc: 'Professionelle Praxis-Website mit Behandlungsübersicht und Online-Terminbuchung.' },
		{ src: '/projekte/green-vista.png',  cat: 'Immobilien',        title: 'Green Vista',   desc: 'Stilvolle Immobilien-Präsentation mit interaktiver Objektsuche und Galerie.' },
		{ src: '/projekte/swiss-vita.png',   cat: 'Gesundheit',        title: 'Swiss Vita',    desc: 'Moderne Wellness-Website mit Angeboten, Terminbuchung und Kundenstimmen.' },
	];

	const testimonials = [
		{ name: 'Klaus Berger',    role: 'Bäckerei Berger, Zürich',      text: '„Seit wir unsere neue Website haben, melden sich viel mehr Kunden. Unkompliziert, schnell, und das Ergebnis übertrifft unsere Erwartungen."' },
		{ name: 'Sandra Hoffmann', role: 'Beauty Studio Lena, Bern',     text: '„Persönliche Betreuung von Anfang bis Ende. Immer jemand erreichbar, der erklärt was passiert. Klare Empfehlung."' },
		{ name: 'Markus Huber',    role: 'Schreinerei Huber, Basel',     text: '„Professionell, pünktlich und preiswert. Die Website sieht genau so aus, wie ich es mir vorgestellt habe."' }
	];
</script>

<svelte:head>
	<title>Referenzen – Aura</title>
</svelte:head>

<div class="page-hero">
	<div class="container">
		<span class="eyebrow">Unsere Arbeiten</span>
		<h1>Referenzen</h1>
		<p>Websites, die wir für Schweizer Unternehmen realisiert haben.</p>
	</div>
</div>

<section class="section section-black">
	<div class="container">
		<div class="proj-grid">
			{#each projects as p, i}
				<div class="proj-card" use:reveal={{ delay: (i % 3) * 60 }}>
					<div class="proj-img">
						<img src={p.src} alt={p.title} loading="lazy" />
					</div>
					<div class="proj-info">
						<span class="proj-cat">{p.cat}</span>
						<h3>{p.title}</h3>
						<p>{p.desc}</p>
					</div>
				</div>
			{/each}
		</div>
	</div>
</section>

<div class="img-strip">
	<img
		src="https://images.unsplash.com/photo-1497366216548-37526070297c?w=1920&q=80&auto=format&fit=crop"
		alt="Modernes Büro"
		loading="lazy"
	/>
	<div class="img-strip-overlay"></div>
</div>

<section class="section section-dark">
	<div class="container">
		<div class="t-header" use:reveal>
			<span class="eyebrow">Kundenstimmen</span>
			<h2 class="display t-title">Was unsere Kunden sagen</h2>
		</div>
		<div class="t-grid">
			{#each testimonials as t, i}
				<div class="t-card" use:reveal={{ delay: i * 80 }}>
					<p class="t-text">{t.text}</p>
					<footer>
						<span class="t-name">{t.name}</span>
						<span class="t-role">{t.role}</span>
					</footer>
				</div>
			{/each}
		</div>
	</div>
</section>

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
	/* Projects */
	.proj-grid {
		display: grid;
		grid-template-columns: repeat(3, 1fr);
		gap: 1px;
		background: rgba(255,255,255,0.05);
		border: 1px solid rgba(255,255,255,0.05);
	}
	.proj-card {
		background: var(--black);
		transition: background 0.3s;
		display: flex;
		flex-direction: column;
	}
	.proj-card:hover { background: #0e0e14; }

	.proj-img { overflow: hidden; }
	.proj-img img { width: 100%; aspect-ratio: 16/10; object-fit: cover; display: block; transition: transform 0.4s cubic-bezier(0.16,1,0.3,1); }
	.proj-card:hover .proj-img img { transform: scale(1.03); }

	.proj-info { padding: 1.25rem 1.5rem 1.5rem; flex: 1; display: flex; flex-direction: column; gap: 0.4rem; }
	.proj-cat { font-size: 0.62rem; font-weight: 500; letter-spacing: 0.16em; text-transform: uppercase; color: #c9a96e; }
	.proj-info h3 { font-size: 1rem; font-weight: 600; color: rgba(255,255,255,0.92); }
	.proj-info p { font-size: 0.83rem; color: rgba(195,212,228,0.78); line-height: 1.65; margin-top: 0.25rem; }

	/* Testimonials */
	.t-header { margin-bottom: 3rem; }
	.t-title { font-size: clamp(2rem, 4vw, 3rem); color: #fff; margin-top: 0.25rem; }
	.t-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 1px; background: rgba(255,255,255,0.05); border: 1px solid rgba(255,255,255,0.05); }
	.t-card { background: var(--dark); padding: 2.5rem 2rem; display: flex; flex-direction: column; justify-content: space-between; gap: 2rem; }
	.t-text { font-size: 0.9rem; color: rgba(195,212,228,0.82); line-height: 1.8; font-style: italic; }
	.t-card footer { display: flex; flex-direction: column; gap: 0.3rem; }
	.t-name { font-size: 0.85rem; font-weight: 500; color: rgba(255,255,255,0.85); }
	.t-role { font-size: 0.75rem; color: rgba(255,255,255,0.5); }

	@media (max-width: 900px) {
		.proj-grid { grid-template-columns: 1fr 1fr; }
		.t-grid { grid-template-columns: 1fr; }
	}
	@media (max-width: 600px) {
		.proj-grid { grid-template-columns: 1fr; }
	}
</style>
