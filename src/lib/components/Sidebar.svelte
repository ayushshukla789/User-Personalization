<script lang="ts">
	import { fly } from 'svelte/transition';

	let { class: customClass = '', navigate = () => {}, selectedView = 'list' } = $props();

	const navigateTo = (view: 'new' | 'list') => {
		navigate(view);
	};
</script>

<aside
	class="rounded-md bg-white p-2 shadow-md {customClass} md:w-64"
	in:fly={{ duration: 300, x: -300 }}
>
	<nav>
		<ul class="space-y-2">
			<li>
				<button
					class="block w-full rounded-md p-4 text-left font-medium text-slate-600 hover:bg-indigo-50 {selectedView ===
					'new'
						? 'bg-white text-indigo-600'
						: ''}"
					onclick={() => navigateTo('new')}>Create New Ad</button
				>
			</li>
			<li>
				<button
					class="block w-full rounded-md p-4 text-left font-medium text-slate-600 hover:bg-indigo-50 {selectedView ===
					'list'
						? 'bg-white text-indigo-600'
						: ''}"
					onclick={() => navigateTo('list')}>View Previous Ads</button
				>
			</li>
		</ul>
	</nav>
</aside>
