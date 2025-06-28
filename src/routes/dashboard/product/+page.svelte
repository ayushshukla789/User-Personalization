<script lang="ts">
	import { page } from '$app/state';
	import { goto } from '$app/navigation';

	interface Categories {
		l1: string;
		l2: string;
		l3: string;
	}

	interface SubmittedData {
		productName: string;
		imageUrls: string[];
		genericName: string;
		brandName: string;
		categories: Categories;
		gender: string;
		cityTier: string;
		response: any;
	}

	interface PageState {
		submittedData: SubmittedData;
	}

	const submittedData = (page.state as PageState).submittedData;

	const handleBack = () => {
		goto('/dashboard');
	};
</script>

<div class="w-full max-w-4xl mx-auto p-6">
	<div class="bg-white rounded-lg shadow-lg p-6">
		<div class="flex justify-between items-center mb-6">
			<h1 class="text-2xl font-bold text-slate-800">Ad Information</h1>
			<button
				onclick={handleBack}
				class="px-4 py-2 text-sm font-medium text-white bg-indigo-600 rounded-md hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
			>
				Back to Dashboard
			</button>
		</div>

		<div class="space-y-6">
			<div class="bg-slate-50 border border-slate-200 rounded-md p-4">
				<h2 class="text-lg font-semibold text-slate-800 mb-2">Product Details</h2>
				<div class="grid grid-cols-1 md:grid-cols-2 gap-4">
					<div>
						<p class="text-sm font-medium text-slate-600">Product Name</p>
						<p class="mt-1">{submittedData.productName || 'N/A'}</p>
					</div>
					<div>
						<p class="text-sm font-medium text-slate-600">Generic Name</p>
						<p class="mt-1">{submittedData.genericName}</p>
					</div>
					<div>
						<p class="text-sm font-medium text-slate-600">Brand Name</p>
						<p class="mt-1">{submittedData.brandName}</p>
					</div>
					<div>
						<p class="text-sm font-medium text-slate-600">Gender</p>
						<p class="mt-1">{submittedData.gender}</p>
					</div>
					<div>
						<p class="text-sm font-medium text-slate-600">City Tier</p>
						<p class="mt-1">{submittedData.cityTier}</p>
					</div>
				</div>
			</div>

			<div class="bg-slate-50 border border-slate-200 rounded-md p-4">
				<h2 class="text-lg font-semibold text-slate-800 mb-2">Categories</h2>
				<div class="space-y-2">
					{#if submittedData.categories.l1}
						<div>
							<p class="text-sm font-medium text-slate-600">Category L1</p>
							<p class="mt-1">{submittedData.categories.l1}</p>
						</div>
					{/if}
					{#if submittedData.categories.l2}
						<div>
							<p class="text-sm font-medium text-slate-600">Category L2</p>
							<p class="mt-1">{submittedData.categories.l2}</p>
						</div>
					{/if}
					{#if submittedData.categories.l3}
						<div>
							<p class="text-sm font-medium text-slate-600">Category L3</p>
							<p class="mt-1">{submittedData.categories.l3}</p>
						</div>
					{/if}
				</div>
			</div>

			{#if submittedData.imageUrls.length > 0}
				<div class="bg-slate-50 border border-slate-200 rounded-md p-4">
					<h2 class="text-lg font-semibold text-slate-800 mb-2">Product Images</h2>
					<div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4">
						{#each submittedData.imageUrls as imageUrl}
							{#if imageUrl}
								<div class="aspect-square rounded-md overflow-hidden">
									<img src={imageUrl} alt="Product" class="w-full h-full object-cover" />
								</div>
							{/if}
						{/each}
					</div>
				</div>
			{/if}

			<div class="bg-slate-50 border border-slate-200 rounded-md p-4">
				<h2 class="text-lg font-semibold text-slate-800 mb-2">API Response</h2>
				<pre class="whitespace-pre-wrap text-sm">{JSON.stringify(submittedData.response, null, 2)}</pre>
			</div>
		</div>
	</div>
</div> 