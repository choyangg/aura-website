<script>
	import { fly } from 'svelte/transition';
	import { cubicOut } from 'svelte/easing';

	// ── Step management ──────────────────────────────────────
	let step = $state(1);
	const TOTAL = 6;
	let dir = $state(1); // 1 = forward, -1 = back

	// ── Form data ────────────────────────────────────────────
	let branche          = $state('');
	let brancheFreitext  = $state('');
	let hasWebsite    = $state('');   // 'ja' | 'nein'
	let websiteUrl    = $state('');
	let ziele            = $state([]);
	let zielSonstiges    = $state('');
	let budget        = $state('');
	let budgetUnsicher = $state(false);
	let vorname       = $state('');
	let nachname      = $state('');
	let email         = $state('');
	let telefon       = $state('');
	let firma         = $state('');

	// ── UI state ─────────────────────────────────────────────
	let showCal   = $state(false);
	let formSent  = $state(false);
	let showErr   = $state(false);
	let formError = $state('');

	// ── Calendar ─────────────────────────────────────────────
	const MONTHS = ['Januar','Februar','März','April','Mai','Juni','Juli','August','September','Oktober','November','Dezember'];
	const TIMES  = ['09:00','10:00','11:00','12:00','14:00','15:00','16:00','17:00','18:00'];
	const _now   = new Date();
	let calMonth = $state(_now.getMonth());
	let calYear  = $state(_now.getFullYear());
	let selDate  = $state(null);
	let selTime  = $state('');
	let booked   = $state(false);

	function calDays(y, m)    { return new Date(y, m+1, 0).getDate(); }
	function calOffset(y, m)  { const d = new Date(y, m, 1).getDay(); return d === 0 ? 6 : d-1; }
	function isPast(y, m, d)  { const t = new Date(); t.setHours(0,0,0,0); return new Date(y,m,d) < t; }
	function isWeekend(y,m,d) { const w = new Date(y,m,d).getDay(); return w===0||w===6; }
	function calCanPrev()     { return !(calYear===_now.getFullYear()&&calMonth===_now.getMonth()); }
	function calCanNext()     { return new Date(calYear,calMonth+1,1)<new Date(_now.getFullYear(),_now.getMonth()+3,1); }
	function calPrev()        { if(!calCanPrev())return; if(calMonth===0){calMonth=11;calYear--;}else calMonth--; }
	function calNext()        { if(!calCanNext())return; if(calMonth===11){calMonth=0;calYear++;}else calMonth++; }
	function calSelect(d)     { if(isPast(calYear,calMonth,d)||isWeekend(calYear,calMonth,d))return; selDate={y:calYear,m:calMonth,d}; selTime=''; }

	async function confirmAppointment() {
		if (!selDate || !selTime) return;
		const brancheLabel = branche === 'Sonstiges' ? `Sonstiges: ${brancheFreitext}` : branche;
		const d  = new Date(selDate.y, selDate.m, selDate.d);
		const ds = d.toLocaleDateString('de-CH', {weekday:'long', day:'numeric', month:'long'});

		const formData = new FormData();
		formData.append('form-name', 'projekt-anfrage');
		formData.append('branche', brancheLabel);
		formData.append('website', hasWebsite === 'ja' ? (websiteUrl || 'Ja') : 'Nein');
		formData.append('ziele', ziele.join(', ') + (zielSonstiges ? ` / ${zielSonstiges}` : ''));
		formData.append('budget', budgetUnsicher ? 'Noch unsicher' : (budget || 'keine Angabe'));
		formData.append('vorname', vorname);
		formData.append('nachname', nachname);
		formData.append('email', email);
		formData.append('telefon', telefon);
		formData.append('termin', ds);
		formData.append('uhrzeit', `${selTime} Uhr`);

		try {
			const response = await fetch('/netlify-form.html', {
				method: 'POST',
				headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
				body: new URLSearchParams(formData).toString()
			});
			if (response.ok) {
				console.log('✅ Netlify hat es empfangen');
			} else {
				console.log('❌ Fehler:', response.status);
			}
		} catch (error) {
			console.error('❌ Fehler:', error);
		}
		booked = true;
		formSent = true;
	}

	// ── Navigation ───────────────────────────────────────────
	function goNext() {
		if (!canContinue()) { showErr = true; return; }
		showErr = false;
		dir = 1;
		step = Math.min(step + 1, TOTAL);
	}
	function goBack() { showErr = false; dir = -1; step = Math.max(step-1, 1); }

	// ── Validation ───────────────────────────────────────────
	function canContinue() {
		if (step===1) return !!branche && (branche !== 'Sonstiges' || brancheFreitext.trim().length > 0);
		if (step===2) return !!hasWebsite;
		if (step===3) return ziele.length > 0;
		if (step===4) return budgetUnsicher || !!budget.trim();
		if (step===5) return !!(vorname.trim() && nachname.trim() && email.trim().includes('@') && telefon.trim());
		return true;
	}

	function toggleZiel(z) {
		ziele = ziele.includes(z) ? ziele.filter(x => x!==z) : [...ziele, z];
	}

	// ── Zeige Kalender (E-Mail wird erst nach Terminwahl gesendet) ──
	function submitAndScroll() {
		showCal = true;
		scrollToCalendar();
	}

	function scrollToCalendar() {
		setTimeout(() => document.getElementById('kalender')?.scrollIntoView({behavior:'smooth', block:'start'}), 120);
	}

	// ── Options ──────────────────────────────────────────────
	const branchen = [
		'Handwerk & Bau',
		'Immobilien',
		'Beauty & Gesundheit',
		'Beratung & Coaching',
		'Gastronomie & Handel',
		'Sonstiges',
	];

	const zielOptions = [
		'Mehr Kundenanfragen gewinnen',
		'Professioneller wirken',
		'Bessere Darstellung meiner Leistungen',
		'Weiss ich noch nicht genau',
		'Sonstiges',
	];
</script>

<svelte:head>
	<title>Projekt starten – Aura</title>
</svelte:head>

<!-- ══ HERO ══ -->
<div class="page-hero">
	<div class="container">
		<span class="eyebrow">Projekt starten</span>
		<h1>Wir freuen uns von Ihnen zu hören.</h1>
		<p style="white-space: nowrap;">Erzählen Sie uns von Ihrem Projekt. Wir melden uns innerhalb von 24 Stunden.</p>
		<p class="trust-note">
			<span>Kostenlos &amp; unverbindlich</span>
			<span class="trust-sep">·</span>
			<span>Antwort innerhalb 24 h</span>
			<span class="trust-sep">·</span>
			<span>Kein Verkaufsdruck</span>
		</p>
	</div>
</div>

<!-- ══ MULTI-STEP FORM ══ -->
<section class="section section-black form-section">
	<div class="container">
		<div class="form-card">

			<!-- Progress bar -->
			<div class="prog-wrap">
				<div class="prog-track">
					<div class="prog-fill" style="width:{((step-1)/(TOTAL-1))*100}%"></div>
				</div>
				<span class="prog-label">Schritt {step} von {TOTAL}</span>
			</div>

			<!-- Step content (keyed for animation) -->
			{#key step}
				<div
					class="step-pane"
					in:fly={{ y: dir*28, duration:400, opacity:0, easing:cubicOut }}
				>

					<!-- ── 1: Branche ── -->
					{#if step===1}
						<h2 class="step-q">In welcher Branche sind Sie tätig?</h2>
						<p class="step-hint">Falls Sie mehrere Bereiche anbieten, wählen Sie den mit dem grössten Umsatzanteil.</p>
						<div class="branch-list">
							{#each branchen as b}
								<button class="branch-row" class:sel={branche===b}
									onclick={()=>branche=b} type="button">
									{b}
								</button>
							{/each}
						</div>
						{#if branche==='Sonstiges'}
							<div class="field-wrap mt" in:fly={{y:10,duration:260}}>
								<input type="text" placeholder="Bitte kurz beschreiben (z.B. Architekturbüro, Fotograf, ...)" bind:value={brancheFreitext} />
							</div>
						{/if}

					<!-- ── 2: Website ── -->
					{:else if step===2}
						<h2 class="step-q">Hast du aktuell eine Website?</h2>
						<div class="choice-grid">
							<button class="choice-card" class:sel={hasWebsite==='ja'}
								onclick={()=>hasWebsite='ja'} type="button">
								Ja, ich habe eine Website
							</button>
							<button class="choice-card" class:sel={hasWebsite==='nein'}
								onclick={()=>hasWebsite='nein'} type="button">
								Nein, noch keine
							</button>
						</div>
						{#if hasWebsite==='ja'}
							<div class="url-wrap" in:fly={{y:10, duration:260}}>
								<label for="url">Deine aktuelle Website-Adresse</label>
								<input id="url" type="url" placeholder="https://www.deinefirma.ch" bind:value={websiteUrl} />
							</div>
						{/if}

					<!-- ── 3: Ziele ── -->
					{:else if step===3}
						<h2 class="step-q">Was wünschst du dir von einer neuen Website?</h2>
						<p class="step-hint">Mehrfachauswahl möglich.</p>
						<div class="goal-list">
							{#each zielOptions as z}
								<button class="goal-item" class:sel={ziele.includes(z)}
									onclick={()=>toggleZiel(z)} type="button">
									<span class="goal-check" aria-hidden="true">{ziele.includes(z)?'✓':''}</span>
									<span>{z}</span>
								</button>
							{/each}
						</div>
						{#if ziele.includes('Sonstiges')}
							<div class="field-wrap mt" in:fly={{y:10,duration:260}}>
								<input type="text" placeholder="Was schwebt dir vor?" bind:value={zielSonstiges} />
							</div>
						{/if}

					<!-- ── 4: Budget ── -->
					{:else if step===4}
						<h2 class="step-q">Welches Budget hast du für deine neue Website eingeplant?</h2>
						<p class="step-hint">Diese Angabe hilft uns, eine passende Strategie zu empfehlen.</p>
						<div class="field-wrap">
							<label for="f-budget">Dein Budget</label>
							<input id="f-budget" type="text" placeholder="Dein Budget" bind:value={budget} onchange={e=>budget=e.target.value} />
						</div>

					<!-- ── 5: Kontaktdaten ── -->
					{:else if step===5}
						<h2 class="step-q">Wie können wir dich erreichen?</h2>
						<p class="step-hint">Deine Daten werden vertraulich behandelt und nicht weitergegeben.</p>
						<div class="contact-fields">
							<div class="row-2">
								<div class="field">
									<label for="f-vorname">Vorname <span class="req">*</span></label>
									<input id="f-vorname" type="text" placeholder="Max"
									class:err={showErr && !vorname.trim()}
									oninput={e=>vorname=e.currentTarget.value} value={vorname} />
								</div>
								<div class="field">
									<label for="f-nachname">Nachname <span class="req">*</span></label>
									<input id="f-nachname" type="text" placeholder="Mustermann"
										class:err={showErr && !nachname.trim()}
										oninput={e=>nachname=e.currentTarget.value} value={nachname} />
								</div>
							</div>
							<div class="row-2">
								<div class="field">
									<label for="f-email">E-Mail <span class="req">*</span></label>
									<input id="f-email" type="text" placeholder="max@firma.ch"
										class:err={showErr && !email.trim().includes('@')}
										oninput={e=>email=e.currentTarget.value} value={email} />
								</div>
								<div class="field">
									<label for="f-telefon">Telefon <span class="req">*</span></label>
									<input id="f-telefon" type="tel" placeholder="+41 76 123 45 67"
										class:err={showErr && !telefon.trim()}
										oninput={e=>telefon=e.currentTarget.value} value={telefon} />
								</div>
							</div>
							<div class="field">
								<label for="f-firma">Firma <span class="opt">(optional)</span></label>
								<input id="f-firma" type="text" placeholder="Muster GmbH" bind:value={firma} />
							</div>
						</div>

					<!-- ── 6: Zusammenfassung ── -->
					{:else if step===6}
						<h2 class="step-q">Fast geschafft. Bitte prüfe deine Angaben.</h2>
						<div class="summary">
							<div class="sum-row">
								<span class="sum-key">Branche</span>
								<span class="sum-val">{branche}</span>
							</div>
							<div class="sum-row">
								<span class="sum-key">Website</span>
								<span class="sum-val">{hasWebsite==='ja' ? (websiteUrl||'Ja') : 'Noch keine'}</span>
							</div>
							<div class="sum-row">
								<span class="sum-key">Ziele</span>
								<span class="sum-val">{ziele.join(' · ')}{zielSonstiges ? ' · '+zielSonstiges : ''}</span>
							</div>
							<div class="sum-row">
								<span class="sum-key">Budget</span>
								<span class="sum-val">{budget}</span>
							</div>
							<div class="sum-row">
								<span class="sum-key">Name</span>
								<span class="sum-val">{vorname} {nachname}</span>
							</div>
							<div class="sum-row">
								<span class="sum-key">E-Mail</span>
								<span class="sum-val">{email}</span>
							</div>
							<div class="sum-row">
								<span class="sum-key">Telefon</span>
								<span class="sum-val">{telefon}</span>
							</div>
							{#if firma}
							<div class="sum-row">
								<span class="sum-key">Firma</span>
								<span class="sum-val">{firma}</span>
							</div>
							{/if}
						</div>
						{#if formError}
							<p class="form-error">{formError}</p>
						{/if}
						<p class="trust-line">Kein Verkaufsdruck. Keine Verpflichtung.<br />Wir hören zu, bevor wir ein Angebot machen.</p>
					{/if}

				</div><!-- /step-pane -->
			{/key}

			<!-- Back / Next buttons -->
			<div class="step-nav">
				{#if step > 1}
					<button class="btn-back" onclick={goBack} type="button">← Zurück</button>
				{:else}
					<span></span>
				{/if}

				{#if step < TOTAL}
					<button
						class="btn-next"
						class:inactive={!canContinue()}
						onclick={goNext}
						type="button"
					>Weiter →</button>
				{:else}
					<button
						class="btn-next btn-book"
						onclick={submitAndScroll}
						type="button"
					>Termin wählen →</button>
				{/if}
			</div>

		</div><!-- /form-card -->
	</div>
</section>

<!-- ══ KALENDER (erscheint nach Terminwahl) ══ -->
{#if showCal}
	<section
		class="section section-dark cal-section"
		id="kalender"
		in:fly={{y:30, duration:500, easing:cubicOut}}
	>
		<div class="container">

			<div class="cal-head">
				{#if booked}
					<h2 class="display cal-title">Vielen Dank für<br /><em>Ihre Anfrage.</em></h2>
					<p class="cal-sub">Wir melden uns innerhalb von 24 Stunden bei Ihnen.</p>
				{:else}
					<h2 class="display cal-title">Ihr kostenloses<br /><em>Erstgespräch buchen.</em></h2>
					<p class="cal-sub">Wählen Sie einen Termin. Wir melden uns zur Bestätigung innerhalb von 24 Stunden.</p>
				{/if}
			</div>

			{#if booked}
				<div class="booked-msg" in:fly={{y:10, duration:300}}>
					<div class="booked-icon">✓</div>
					<h3>Terminanfrage gesendet!</h3>
					<p>Wir bestätigen Ihren Termin per E-Mail.<br />Bis bald. Wir freuen uns auf das Gespräch.</p>
				</div>
			{:else}
				<div class="cal-wrap">
					<div class="cal-nav-row">
						<button class="cal-arrow" onclick={calPrev} disabled={!calCanPrev()} aria-label="Vorheriger Monat">←</button>
						<span class="cal-month-label">{MONTHS[calMonth]} {calYear}</span>
						<button class="cal-arrow" onclick={calNext} disabled={!calCanNext()} aria-label="Nächster Monat">→</button>
					</div>
					<div class="cal-grid">
						{#each ['Mo','Di','Mi','Do','Fr','Sa','So'] as h}
							<div class="cal-head-cell">{h}</div>
						{/each}
						{#each Array(calOffset(calYear,calMonth)) as _}<div></div>{/each}
						{#each Array(calDays(calYear,calMonth)) as _, i}
							{@const day=i+1}
							{@const off=isPast(calYear,calMonth,day)||isWeekend(calYear,calMonth,day)}
							{@const isSel=selDate?.y===calYear&&selDate?.m===calMonth&&selDate?.d===day}
							<button class="cal-day" class:off class:isSel
								onclick={()=>calSelect(day)} disabled={off} type="button">{day}</button>
						{/each}
					</div>
					{#if selDate}
						<div class="time-row" in:fly={{y:10,duration:260}}>
							<label class="time-label" for="cal-time">Uhrzeit wählen</label>
							<select id="cal-time" class="time-select" bind:value={selTime}>
								<option value="" disabled>-- Uhrzeit --</option>
								{#each TIMES as t}<option value={t}>{t} Uhr</option>{/each}
							</select>
						</div>
					{/if}
				</div>

				<div class="cal-confirm">
					{#if selDate&&selTime}
						{@const d=new Date(selDate.y,selDate.m,selDate.d)}
						<p class="sel-info">Gewählt: <strong>{d.toLocaleDateString('de-CH',{weekday:'short',day:'numeric',month:'short'})} · {selTime} Uhr</strong></p>
					{/if}
					<button
						class="confirm-btn"
						class:active={!!(selDate&&selTime)}
						onclick={confirmAppointment}
						disabled={!selDate||!selTime}
						type="button"
					>{selDate&&selTime ? 'Anfrage absenden →' : 'Datum & Uhrzeit wählen'}</button>
					<p class="cal-note">Wir bestätigen Ihren Wunschtermin innerhalb von 24 Stunden.</p>
				</div>
			{/if}

		</div>
	</section>
{/if}

<style>
	/* ── Trust note in hero ── */
	.trust-note {
		display: flex; align-items: center; gap: 0.75rem;
		font-size: 0.78rem; font-weight: 500; letter-spacing: 0.08em;
		color: rgba(200,220,238,0.65); margin-top: 1.25rem; white-space: nowrap;
	}
	.trust-sep { color: rgba(168,216,240,0.3); }

	/* ── Form section ── */
	.form-section { padding-top: 3rem; padding-bottom: 8rem; }
	.form-card {
		max-width: 680px; margin: 0 auto;
		border: 1px solid rgba(255,255,255,0.05);
		padding: 3rem 3.5rem 4rem;
	}

	/* ── Progress ── */
	.prog-wrap { display: flex; align-items: center; gap: 1rem; margin-bottom: 2.75rem; }
	.prog-track { flex: 1; height: 2px; background: rgba(255,255,255,0.08); border-radius: 2px; overflow: hidden; }
	.prog-fill  { height: 100%; background: rgba(168,216,240,0.7); box-shadow: 0 0 8px rgba(168,216,240,0.4); transition: width 0.5s cubic-bezier(0.16,1,0.3,1); }
	.prog-label { font-size: 0.6rem; font-weight: 600; letter-spacing: 0.18em; text-transform: uppercase; color: rgba(255,255,255,0.35); white-space: nowrap; }

	/* ── Step pane ── */
	.step-pane  { min-height: 300px; display: flex; flex-direction: column; }
	.step-q     { font-family: 'Playfair Display', Georgia, serif; font-size: clamp(1.4rem,2.6vw,1.85rem); font-weight: 600; color: #f0f4f8; letter-spacing: -0.02em; line-height: 1.25; margin-bottom: 0.75rem; }
	.step-hint  { font-size: 0.92rem; color: rgba(190,210,228,0.65); line-height: 1.75; margin-bottom: 1.75rem; }

	/* ── Branch list (step 1) ── */
	.branch-list { display: flex; flex-direction: column; gap: 0.4rem; }
	.branch-row {
		width: 100%; padding: 1.25rem 1.5rem; text-align: left;
		background: none; border: none;
		border-left: 3px solid transparent;
		border-bottom: 1px solid rgba(255,255,255,0.05);
		cursor: pointer; transition: all 0.18s;
		font-size: 0.925rem; color: rgba(215,232,248,0.75); line-height: 1.4;
	}
	.branch-row:hover { border-left-color: rgba(168,216,240,0.5); color: #f0f4f8; background: rgba(168,216,240,0.05); }
	.branch-row.sel   { border-left-color: #a8d8f0; color: #fff; background: rgba(168,216,240,0.07); box-shadow: 0 0 8px rgba(168,216,240,0.08); }

	/* ── Choice grid (step 2) ── */
	.choice-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 1rem; margin-bottom: 1.25rem; }
	.choice-card {
		padding: 1.75rem 1.5rem;
		background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.07);
		cursor: pointer; transition: all 0.18s; border-radius: 3px;
		font-size: 0.925rem; color: rgba(220,235,248,0.8); text-align: center;
	}
	.choice-card:hover { border-color: rgba(168,216,240,0.28); background: rgba(168,216,240,0.05); color: #f0f4f8; }
	.choice-card.sel   { border-color: rgba(168,216,240,0.7); background: rgba(168,216,240,0.07); color: #e0f4ff; box-shadow: 0 0 8px rgba(168,216,240,0.08); }

	.url-wrap       { display: flex; flex-direction: column; gap: 0.5rem; }
	.url-wrap label { font-size: 0.62rem; font-weight: 600; letter-spacing: 0.14em; text-transform: uppercase; color: rgba(255,255,255,0.6); }

	/* ── Generic fields ── */
	.field-wrap    { display: flex; flex-direction: column; gap: 0.5rem; flex: 1; }
	.field-wrap.mt { margin-top: 1rem; }

	input, select {
		padding: 0.875rem 1rem;
		background: rgba(255,255,255,0.05); border: 1px solid rgba(255,255,255,0.13);
		color: #f0f4f8; font-size: 0.875rem; font-weight: 300;
		font-family: 'DM Sans', sans-serif;
		width: 100%; transition: border-color 0.2s; border-radius: 2px;
	}
	input:focus, select:focus { outline: none; border-color: rgba(168,216,240,0.55); box-shadow: 0 0 8px rgba(168,216,240,0.1); }
	input::placeholder { color: rgba(255,255,255,0.25); }
	input.err { border-color: rgba(248,113,113,0.9) !important; box-shadow: 0 0 12px rgba(248,113,113,0.35); background: rgba(248,113,113,0.08) !important; }

	input:disabled { opacity: 0.35; cursor: not-allowed; }

	/* ── Goals (step 4) ── */
	.goal-list { display: flex; flex-direction: column; gap: 0.5rem; }
	.goal-item {
		display: flex; align-items: center; gap: 1rem; padding: 0.875rem 1.25rem;
		background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.07);
		cursor: pointer; transition: all 0.18s; text-align: left; border-radius: 3px;
		font-size: 0.925rem; color: rgba(215,232,248,0.8);
	}
	.goal-item:hover { border-color: rgba(168,216,240,0.22); background: rgba(168,216,240,0.04); }
	.goal-item.sel   { border-color: rgba(168,216,240,0.7); background: rgba(168,216,240,0.07); color: #e0f4ff; box-shadow: 0 0 8px rgba(168,216,240,0.08); }
	.goal-check {
		width: 18px; height: 18px; flex-shrink: 0;
		border: 1px solid rgba(255,255,255,0.2); border-radius: 3px;
		display: flex; align-items: center; justify-content: center;
		font-size: 0.6rem; font-weight: 700; color: #a8d8f0; transition: all 0.15s;
	}
	.goal-item.sel .goal-check { border-color: #a8d8f0; background: rgba(168,216,240,0.15); }

	/* ── Contact fields (step 6) ── */
	.contact-fields { display: flex; flex-direction: column; gap: 1rem; }
	.row-2 { display: grid; grid-template-columns: 1fr 1fr; gap: 1rem; }
	.field { display: flex; flex-direction: column; gap: 0.45rem; }
	label  { font-size: 0.62rem; font-weight: 600; letter-spacing: 0.14em; text-transform: uppercase; color: rgba(255,255,255,0.62); }
	.opt   { color: rgba(255,255,255,0.3); font-weight: 400; text-transform: none; letter-spacing: 0; }
	.req   { color: rgba(168,216,240,0.7); font-weight: 600; }

	/* ── Summary (step 7) ── */
	.summary { display: flex; flex-direction: column; border-top: 1px solid rgba(255,255,255,0.06); margin-bottom: 1.75rem; }
	.sum-row {
		display: flex; gap: 2rem; padding: 0.875rem 0;
		border-bottom: 1px solid rgba(255,255,255,0.05); align-items: flex-start;
	}
	.sum-key { font-size: 0.82rem; font-weight: 600; color: #a8d8f0; min-width: 110px; flex-shrink: 0; padding-top: 0.1rem; }
	.sum-val { font-size: 0.85rem; color: rgba(215,232,248,0.82); line-height: 1.65; }
	.trust-line { font-size: 0.78rem; color: rgba(185,205,225,0.48); line-height: 1.8; font-style: italic; border-top: 1px solid rgba(255,255,255,0.05); padding-top: 1.5rem; }
	.form-error { font-size: 0.82rem; color: #f87171; margin-bottom: 1rem; padding: 0.875rem 1rem; background: rgba(248,113,113,0.07); border: 1px solid rgba(248,113,113,0.2); border-radius: 2px; }

	/* ── Navigation buttons ── */
	.step-nav { display: flex; align-items: center; justify-content: space-between; margin-top: 2.5rem; padding-top: 1.75rem; border-top: 1px solid rgba(255,255,255,0.06); }
	.btn-back { background: none; border: none; cursor: pointer; font-size: 0.68rem; font-weight: 500; letter-spacing: 0.12em; text-transform: uppercase; color: rgba(255,255,255,0.35); transition: color 0.2s; padding: 0; }
	.btn-back:hover { color: rgba(255,255,255,0.7); }
	.btn-next {
		padding: 0.875rem 2.25rem; background: none;
		border: 1px solid rgba(168,216,240,0.35); color: #a8d8f0;
		font-size: 0.7rem; font-weight: 500; letter-spacing: 0.14em; text-transform: uppercase;
		cursor: pointer; transition: all 0.25s; border-radius: 2px;
	}
	.btn-next:hover:not(.inactive):not(:disabled) { background: rgba(168,216,240,0.1); border-color: #a8d8f0; }
	.btn-next.inactive { opacity: 0.3; cursor: not-allowed; }
	.btn-next.btn-book { border-color: rgba(168,216,240,0.35); color: #a8d8f0; padding: 0.875rem 2.5rem; }
	.btn-next.btn-book:hover:not(:disabled) { background: rgba(168,216,240,0.1); border-color: #a8d8f0; }
	.btn-next:disabled { opacity: 0.5; cursor: not-allowed; }

	/* ── Calendar section ── */
	.cal-section { padding-top: 5rem; }
	.cal-head { text-align: center; margin-bottom: 3rem; }
	.cal-title { font-size: clamp(1.75rem,3.5vw,2.75rem); color: #fff; margin-bottom: 0.875rem; }
	.cal-title em { color: #7ec8e3; font-style: italic; }
	.cal-sub { font-size: 0.9rem; color: rgba(185,208,228,0.7); line-height: 1.75; }

	.booked-msg { max-width: 460px; margin: 0 auto; text-align: center; padding: 3rem 2rem; border: 1px solid rgba(74,222,128,0.2); background: rgba(74,222,128,0.04); border-radius: 2px; }
	.booked-icon { width: 52px; height: 52px; border-radius: 50%; border: 1px solid rgba(74,222,128,0.4); color: #4ade80; display: flex; align-items: center; justify-content: center; font-size: 1.25rem; margin: 0 auto 1.25rem; }
	.booked-msg h3 { font-family: 'Playfair Display', Georgia, serif; font-size: 1.4rem; color: #fff; margin-bottom: 0.75rem; }
	.booked-msg p  { font-size: 0.875rem; color: rgba(185,208,228,0.7); line-height: 1.8; }

	.cal-wrap { max-width: 460px; margin: 0 auto 2rem; }
	.cal-nav-row { display: flex; align-items: center; justify-content: space-between; margin-bottom: 1.5rem; }
	.cal-month-label { font-family: 'Playfair Display', Georgia, serif; font-size: 1.05rem; font-weight: 700; color: #fff; }
	.cal-arrow { width: 38px; height: 38px; border-radius: 50%; border: 1px solid rgba(255,255,255,0.16); background: rgba(255,255,255,0.06); color: rgba(255,255,255,0.82); cursor: pointer; display: flex; align-items: center; justify-content: center; transition: all 0.2s; }
	.cal-arrow:hover:not(:disabled) { background: rgba(255,255,255,0.14); border-color: rgba(255,255,255,0.42); }
	.cal-arrow:disabled { opacity: 0.25; cursor: default; }

	.cal-grid { display: grid; grid-template-columns: repeat(7,1fr); gap: 3px; }
	.cal-head-cell { font-size: 0.56rem; font-weight: 600; letter-spacing: 0.1em; text-transform: uppercase; color: rgba(255,255,255,0.42); text-align: center; padding-bottom: 0.625rem; }
	.cal-day { aspect-ratio:1; border-radius:50%; background:none; border:none; font-size:0.8rem; color:rgba(255,255,255,0.8); cursor:pointer; display:flex; align-items:center; justify-content:center; transition:all 0.15s; }
	.cal-day:hover:not(:disabled):not(.off) { background: rgba(168,216,240,0.18); color: #e0f4ff; }
	.cal-day.off  { color: rgba(255,255,255,0.2) !important; cursor: default; }
	.cal-day.isSel { background: #a8d8f0 !important; color: #04040c !important; font-weight: 700; }

	.time-row { margin-top: 1.5rem; display: flex; flex-direction: column; gap: 0.5rem; }
	.time-label { font-size: 0.6rem; font-weight: 600; letter-spacing: 0.16em; text-transform: uppercase; color: rgba(255,255,255,0.52); }
	.time-select { appearance: none; cursor: pointer; background: rgba(255,255,255,0.07); border: 1px solid rgba(255,255,255,0.16); color: rgba(255,255,255,0.9); padding: 0.8rem 1rem; border-radius: 3px; font-size: 0.875rem; transition: border-color 0.2s; }
	.time-select:focus { outline: none; border-color: rgba(168,216,240,0.5); }
	.time-select option { background: #0e0e16; }

	.cal-confirm { max-width: 460px; margin: 0 auto; text-align: center; }
	.sel-info { font-size: 0.82rem; color: rgba(255,255,255,0.62); margin-bottom: 1rem; }
	.sel-info strong { color: #a8d8f0; }
	.confirm-btn { padding: 0.9rem 2.5rem; border: 1px solid rgba(255,255,255,0.12); background: none; color: rgba(255,255,255,0.28); font-size: 0.7rem; font-weight: 500; letter-spacing: 0.14em; text-transform: uppercase; border-radius: 2px; cursor: not-allowed; transition: all 0.3s; }
	.confirm-btn.active { border-color: rgba(168,216,240,0.55); color: #a8d8f0; cursor: pointer; background: rgba(168,216,240,0.07); }
	.confirm-btn.active:hover { background: rgba(168,216,240,0.14); border-color: #a8d8f0; }
	.cal-note { font-size: 0.72rem; color: rgba(255,255,255,0.32); margin-top: 1rem; }

	/* ── Responsive ── */
	@media (max-width: 768px) {
		.form-card   { padding: 2rem 1.5rem 3rem; }
		.branch-row  { font-size: 0.925rem; padding: 1.1rem 1.25rem; }
		.row-2       { grid-template-columns: 1fr; }
		.trust-note  { white-space: normal; gap: 0.5rem; }
		.trust-sep   { display: none; }
		.trust-note span:not(.trust-sep)::after { content: ' ·'; color: rgba(168,216,240,0.3); }
		.trust-note span:last-child::after { content: ''; }
		.step-q      { font-size: clamp(1.2rem, 4vw, 1.6rem); }
	}
	@media (max-width: 480px) {
		.choice-grid { grid-template-columns: 1fr; }
		.sum-key     { min-width: 80px; }
		.form-card   { padding: 1.5rem 1.25rem 2.5rem; }
		.cal-grid    { gap: 2px; }
	}
</style>
