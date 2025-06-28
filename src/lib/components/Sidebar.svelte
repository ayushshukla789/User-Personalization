<script lang="ts">
	import { fly } from 'svelte/transition';

	let { class: customClass = '', navigate = () => {} } = $props();

	const navigateTo = (view: 'new' | 'list') => {
		navigate(view);
	};
</script>

<aside
	class="rounded-md bg-gray-50 p-2 shadow-md {customClass} md:w-64"
	in:fly={{ duration: 300, x: -300 }}
>
	<nav>
		<ul>
			<li>
				<button
					class="block w-full rounded-md p-4 text-left text-slate-600 hover:bg-indigo-100"
					onclick={() => navigateTo('new')}>+ Create New Ad</button
				>
			</li>
			<li>
				<button
					class="block w-full rounded-md p-4 text-left text-slate-600 hover:bg-indigo-100"
					onclick={() => navigateTo('list')}>View Previous Ads</button
				>
			</li>
		</ul>
	</nav>
</aside>
