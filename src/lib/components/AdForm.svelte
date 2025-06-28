<script lang="ts">
	// Product details
	let productName: string = '';
	let imageUrls: string[] = [''];
	let selectedL1Category: string = '';
	let selectedL2Category: string = '';
	let selectedL3Category: string = '';
	let selectedGenericName: string = '';
	let selectedGenders: string[] = [];
	let brandName: string = '';

	// Fixed options with proper typing
	interface Categories {
		L1: string[];
		L2: { [key: string]: string[] };
		L3: { [key: string]: string[] };
	}

	const categories: Categories = {
		L1: ['Electronics', 'Fashion', 'Home & Living', 'Beauty'],
		L2: {
			Electronics: ['Smartphones', 'Laptops', 'Accessories'],
			Fashion: ['Clothing', 'Footwear', 'Accessories'],
			'Home & Living': ['Furniture', 'Decor', 'Kitchen'],
			Beauty: ['Skincare', 'Makeup', 'Haircare']
		},
		L3: {
			Smartphones: ['Basic Phones', 'Feature Phones', 'Smart Phones'],
			Laptops: ['Gaming', 'Business', 'Student'],
			Clothing: ['Tops', 'Bottoms', 'Dresses']
		}
	};

	const genericNames = ['Phone', 'Laptop', 'T-Shirt', 'Jeans', 'Watch', 'Bag'];
	const genderOptions = ['Male', 'Women', 'Unisex'];

	let loading: boolean = false;
	let error: string | null = null;
	let success: string | null = null;

	// Add a new image URL field
	const addImageUrl = () => {
		imageUrls = [...imageUrls, ''];
	};

	// Remove an image URL field
	const removeImageUrl = (index: number) => {
		imageUrls = imageUrls.filter((_, i) => i !== index);
	};

	// Toggle gender selection
	const toggleGender = (gender: string) => {
		if (selectedGenders.includes(gender)) {
			selectedGenders = selectedGenders.filter((g) => g !== gender);
		} else {
			selectedGenders = [...selectedGenders, gender];
		}
	};

	const handleSubmit = async () => {
		loading = true;
		error = null;
		success = null;

		// Validate required fields
		if (
			!productName ||
			imageUrls.some((url) => !url) ||
			!selectedL1Category ||
			!selectedL2Category ||
			!selectedL3Category ||
			!selectedGenericName ||
			selectedGenders.length === 0 ||
			!brandName
		) {
			error = 'Please fill in all required fields.';
			loading = false;
			return;
		}

		try {
			// In a real application, this would be an actual API call to create a product
			console.log('Creating product:', {
				productName,
				imageUrls,
				categories: {
					L1: selectedL1Category,
					L2: selectedL2Category,
					L3: selectedL3Category
				},
				genericName: selectedGenericName,
				genders: selectedGenders,
				brandName
			});
			success = 'Product created successfully!';

			// Reset form
			productName = '';
			imageUrls = [''];
			selectedL1Category = '';
			selectedL2Category = '';
			selectedL3Category = '';
			selectedGenericName = '';
			selectedGenders = [];
			brandName = '';
		} catch {
			error = 'Failed to create product.';
		}

		loading = false;
	};
</script>

<div class="w-full">
	<h2 class="mb-4 text-2xl font-bold text-slate-800">Create New Product</h2>

	{#if success}
		<div class="mb-4 rounded-md bg-green-100 p-3 text-green-700">{success}</div>
	{/if}
	{#if error}
		<div class="mb-4 rounded-md bg-red-100 p-3 text-red-700">{error}</div>
	{/if}

	<form on:submit|preventDefault={handleSubmit} class="space-y-4">
		<div>
			<label for="productName" class="block text-sm font-medium text-slate-700">Product Name</label>
			<input
				type="text"
				id="productName"
				bind:value={productName}
				class="mt-1 block w-full rounded-md border-slate-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
				required
			/>
		</div>

		<!-- Image URLs -->
		<div>
			<span class="block text-sm font-medium text-slate-700">Product Images</span>
			<div class="mt-1 space-y-4">
				{#each imageUrls as imageUrl, index}
					<div class="flex flex-col gap-2 md:flex-row md:gap-4">
						<input
							type="url"
							bind:value={imageUrl}
							placeholder="Image URL"
							class="mt-1 block w-full rounded-md border-slate-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
							required
						/>
						{#if index > 0}
							<button
								type="button"
								on:click={() => removeImageUrl(index)}
								class="mt-1 w-full min-w-44 rounded-md bg-red-500 px-3 py-2 text-nowrap text-white hover:bg-red-600 md:w-auto"
							>
								Remove
							</button>
						{:else}
							<button
								type="button"
								on:click={addImageUrl}
								class="mt-1 w-full min-w-44 rounded-md bg-orange-500 px-3 py-2 text-nowrap text-white hover:bg-orange-600 md:w-auto"
							>
								Add Image URL
							</button>
						{/if}
					</div>
				{/each}
			</div>
		</div>

		<div class="flex w-full flex-col gap-4 md:flex-row">
			<!-- Generic Name -->
			<div class="flex-1">
				<label for="genericName" class="block text-sm font-medium text-slate-600"
					>Generic Name</label
				>
				<select
					id="genericName"
					bind:value={selectedGenericName}
					class="mt-1 block w-full rounded-md border-slate-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
					required
				>
					<option value="">Select Generic Name</option>
					{#each genericNames as name}
						<option value={name}>{name}</option>
					{/each}
				</select>
			</div>

			<!-- Brand Name -->
			<div class="flex-1">
				<label for="brandName" class="block text-sm font-medium text-slate-600">Brand Name</label>
				<input
					type="text"
					id="brandName"
					bind:value={brandName}
					class="mt-1 block w-full rounded-md border-slate-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
					required
				/>
			</div>
		</div>

		<!-- Categories -->
		<div class="flex w-full flex-col gap-4 space-y-4 md:flex-row">
			<div class="flex-1">
				<label for="l1category" class="block text-sm font-medium text-slate-600">Category L1</label>
				<select
					id="l1category"
					bind:value={selectedL1Category}
					class="mt-1 block w-full rounded-md border-slate-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
					required
				>
					<option value="">Select L1 Category</option>
					{#each categories.L1 as category}
						<option value={category}>{category}</option>
					{/each}
				</select>
			</div>

			<div class="flex-1">
				<label for="l2category" class="block text-sm font-medium text-slate-600">Category L2</label>
				<select
					id="l2category"
					bind:value={selectedL2Category}
					class="mt-1 block w-full rounded-md border-slate-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 disabled:cursor-not-allowed disabled:bg-slate-100"
					required
					disabled={!selectedL1Category}
				>
					<option value=""
						>{selectedL1Category ? 'Select L2 Category' : 'Please select Category L1 first'}</option
					>
					{#if selectedL1Category && categories.L2[selectedL1Category]}
						{#each categories.L2[selectedL1Category] as category}
							<option value={category}>{category}</option>
						{/each}
					{/if}
				</select>
			</div>

			<div class="flex-1">
				<label for="l3category" class="block text-sm font-medium text-slate-600">Category L3</label>
				<select
					id="l3category"
					bind:value={selectedL3Category}
					class="mt-1 block w-full rounded-md border-slate-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 disabled:cursor-not-allowed disabled:bg-slate-100"
					required
					disabled={!selectedL2Category || !categories.L3[selectedL2Category]}
				>
					<option value=""
						>{!selectedL1Category
							? 'Please select Category L1 first'
							: !selectedL2Category
								? 'Please select Category L2 first'
								: 'Select L3 Category'}</option
					>
					{#if selectedL2Category && categories.L3[selectedL2Category]}
						{#each categories.L3[selectedL2Category] as category}
							<option value={category}>{category}</option>
						{/each}
					{/if}
				</select>
			</div>
		</div>

		<div class="flex w-full flex-col gap-4 md:flex-row">
			<!-- Gender Selection -->
			<div class="flex-1">
				<span class="block text-sm font-medium text-slate-700">Gender</span>
				<div class="mt-2 space-x-4">
					{#each genderOptions as gender}
						<label class="inline-flex items-center">
							<input
								type="checkbox"
								value={gender}
								checked={selectedGenders.includes(gender)}
								on:change={() => toggleGender(gender)}
								class="rounded border-slate-300 text-indigo-600 focus:ring-indigo-500"
							/>
							<span class="ml-2">{gender}</span>
						</label>
					{/each}
				</div>
			</div>

			<div class="flex-1">
				<button
					type="submit"
					class="flex h-full w-full items-center justify-center rounded-md border border-transparent bg-indigo-600 px-4 py-2 font-medium text-white shadow-sm hover:bg-indigo-700 focus:ring-0 focus:outline-none disabled:opacity-50"
					disabled={loading}
				>
					{loading ? 'Creating...' : 'Create Product'}
				</button>
			</div>
		</div>
	</form>
</div>
