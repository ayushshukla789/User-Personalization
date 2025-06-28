<script lang="ts">
	let title: string = '';
	let description: string = '';
	let imageUrl: string = '';
	let targetUrl: string = '';
	let loading: boolean = false;
	let error: string | null = null;
	let success: string | null = null;

	const handleSubmit = async () => {
		loading = true;
		error = null;
		success = null;

		// Simulate API call
		await new Promise((resolve) => setTimeout(resolve, 1000));

		if (!title || !description || !imageUrl || !targetUrl) {
			error = 'Please fill in all fields.';
			loading = false;
			return;
		}

		try {
			// In a real application, this would be an actual API call to create an ad
			console.log('Creating ad:', { title, description, imageUrl, targetUrl });
			success = 'Ad created successfully!';
			title = '';
			description = '';
			imageUrl = '';
			targetUrl = '';
		} catch {
			error = 'Failed to create ad.';
		}

		loading = false;
	};
</script>

<div class="w-full">
	<h2 class="mb-4 text-2xl font-bold text-gray-800">Create New Ad</h2>

	{#if success}
		<div class="mb-4 rounded-md bg-green-100 p-3 text-green-700">{success}</div>
	{/if}
	{#if error}
		<div class="mb-4 rounded-md bg-red-100 p-3 text-red-700">{error}</div>
	{/if}

	<form on:submit|preventDefault={handleSubmit} class="space-y-4">
		<div>
			<label for="title" class="block text-sm font-medium text-gray-700">Title</label>
			<input
				type="text"
				id="title"
				bind:value={title}
				class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
				required
			/>
		</div>
		<div>
			<label for="description" class="block text-sm font-medium text-gray-700">Description</label>
			<textarea
				id="description"
				bind:value={description}
				class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
				rows="3"
				required
			></textarea>
		</div>
		<div>
			<label for="imageUrl" class="block text-sm font-medium text-gray-700">Image URL</label>
			<input
				type="url"
				id="imageUrl"
				bind:value={imageUrl}
				class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
				required
			/>
		</div>
		<div>
			<label for="targetUrl" class="block text-sm font-medium text-gray-700">Target URL</label>
			<input
				type="url"
				id="targetUrl"
				bind:value={targetUrl}
				class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
				required
			/>
		</div>
		<button
			type="submit"
			class="inline-flex justify-center rounded-md border border-transparent bg-indigo-600 px-4 py-2 text-sm font-medium text-white shadow-sm hover:bg-indigo-700 focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2 focus:outline-none disabled:opacity-50"
			disabled={loading}
		>
			{loading ? 'Creating...' : 'Create Ad'}
		</button>
	</form>
</div>
