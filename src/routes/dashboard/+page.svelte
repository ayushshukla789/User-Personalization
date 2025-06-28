<script lang="ts">
	import { onMount, onDestroy } from 'svelte';

	import Header from '$lib/components/Header.svelte';
	import Sidebar from '$lib/components/Sidebar.svelte';
	import AdForm from '$lib/components/AdForm.svelte';
	import AdList from '$lib/components/AdList.svelte';

	let currentView: 'new' | 'list' = 'list';
	let showSidebar: boolean = false;

	const handleNavigate = (tab: 'new' | 'list') => {
		currentView = tab;
		showSidebar = false;
	};

	const toggleSidebar = () => {
		showSidebar = !showSidebar;
	};

	const isMobile = () => window.innerWidth < 768;

	const handleScroll = () => {
		if (isMobile() && showSidebar) {
			showSidebar = false;
		}
	};

	onMount(() => {
		window.addEventListener('scroll', handleScroll);
	});

	onDestroy(() => {
		window.removeEventListener('scroll', handleScroll);
	});
</script>

<div class="container">
	<div class="flex-1">
		<Header on:toggleSidebar={toggleSidebar} />
		<main class="mt-20 flex flex-col gap-6 rounded-md bg-white md:flex-row">
			<Sidebar
				class={showSidebar
					? 'absolute top-14 left-2 block w-11/12 '
					: 'hidden h-fit w-full md:block'}
				navigate={handleNavigate}
				selectedView={currentView}
			/>
			{#if currentView === 'new'}
				<AdForm />
			{:else if currentView === 'list'}
				<AdList />
			{/if}
		</main>
	</div>
</div>
