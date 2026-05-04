<script>
	import { page } from '$app/stores';

	let scrolled = $state(false);
	let menuOpen = $state(false);

	$effect(() => {
		if (typeof window === 'undefined') return;
		const fn = () => { scrolled = window.scrollY > 60; };
		window.addEventListener('scroll', fn, { passive: true });
		fn();
		return () => window.removeEventListener('scroll', fn);
	});

	const links = [
		{ href: '/leistungen', label: 'Leistungen' },
		{ href: '/ablauf',     label: 'Ablauf'     },
		{ href: '/referenzen', label: 'Projekte' },
	];

	function close() { menuOpen = false; }
</script>

<nav class:scrolled>
	<div class="container nav-inner">
		<a href="/" class="logo" onclick={close}>
			<img src="/logo.png" alt="AURA" />
		</a>

		<div class="links" class:open={menuOpen}>
			{#each links as l}
				<a href={l.href} class:active={$page.url.pathname.startsWith(l.href)} onclick={close}>
					{l.label}
				</a>
			{/each}
			<a href="/kontakt" class="nav-cta" onclick={close}>Projekt starten →</a>
		</div>

		<button class="burger" onclick={() => menuOpen = !menuOpen} aria-label="Menu">
			<span class:x={menuOpen}></span>
		</button>
	</div>
</nav>

{#if menuOpen}
	<div class="backdrop" onclick={close} role="presentation"></div>
{/if}

<style>
	nav {
		position: fixed; top: 0; left: 0; right: 0; z-index: 300;
		transition: background 0.5s ease, padding 0.4s ease, border-color 0.4s ease;
		padding: 1.75rem 0;
		border-bottom: 1px solid transparent;
	}
	nav.scrolled {
		background: rgba(5,5,10,0.94);
		backdrop-filter: blur(24px) saturate(160%);
		padding: 1rem 0;
		border-bottom-color: rgba(168,216,240,0.1);
	}
	.nav-inner { display: flex; align-items: center; }
	.logo {
		flex: 1; display: flex; align-items: center;
		filter: drop-shadow(0 0 14px rgba(168,216,240,0.18));
		transition: filter 0.3s;
	}
	.logo img { height: 52px; width: auto; display: block; }
	.logo:hover { filter: drop-shadow(0 0 28px rgba(168,216,240,0.45)); }
	.links { display: flex; align-items: center; gap: 0.25rem; }
	.links a:not(.nav-cta) {
		padding: 0.5rem 0.875rem; font-size: 0.72rem; font-weight: 400;
		letter-spacing: 0.12em; text-transform: uppercase;
		color: rgba(168,216,240,0.45); transition: color 0.2s;
	}
	.links a:not(.nav-cta):hover { color: #a8d8f0; }
	.links a.active:not(.nav-cta) { color: #a8d8f0; }
	.nav-cta {
		margin-left: 1.25rem; padding: 0.55rem 1.5rem;
		border: 1px solid rgba(168,216,240,0.28); color: #a8d8f0;
		font-size: 0.68rem; font-weight: 500; letter-spacing: 0.14em;
		text-transform: uppercase; transition: all 0.3s; border-radius: 2px;
	}
	.nav-cta:hover { background: rgba(168,216,240,0.1); border-color: #a8d8f0; }
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
	.burger span::after { top: 6px; }
	.burger span.x { background: transparent; }
	.burger span.x::before { top: 0; transform: rotate(45deg); }
	.burger span.x::after { top: -2px; transform: rotate(-45deg); }
	.backdrop {
		position: fixed; inset: 0; background: rgba(6,6,8,0.6);
		z-index: 290; backdrop-filter: blur(4px);
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
	}
</style>
