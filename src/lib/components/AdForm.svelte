<script lang="ts">
	import { getUsersPersonas } from "$lib/api";
	import { goto } from '$app/navigation';

	// Product details
	let productName: string = '';
	let imageUrls: string[] = [''];
	let selectedL1Category: string = '';
	let selectedL2Category: string = '';
	let selectedL3Category: string = '';
	let selectedGender: string = '';
	let selectedCityTier: string = '';
	let brandName: string = '';

	// Fixed options with proper typing
	interface Categories {
		L1: { id: string; name: string }[];
		L2: { [key: string]: { id: string; name: string }[] };
		L3: { [key: string]: { id: string; name: string }[] };
	}

	const categories: Categories = {
		L1: [
			{ id: "8", name: "Mobiles & Accessories" },
			{ id: "10", name: "Footwear" },
			{ id: "28", name: "Watches" },
			{ id: "36", name: "Beauty and Grooming" },
			{ id: "55", name: "Computers" },
			{ id: "58", name: "Audio & Video" },
			{ id: "71", name: "Home & Kitchen" },
			{ id: "277", name: "Books" }
		],
		L2: {
			"8": [{ id: "9", name: "Mobiles" }],
			"10": [{ id: "88", name: "Women's Footwear" }],
			"28": [{ id: "29", name: "Wrist Watches" }],
			"36": [{ id: "37", name: "Makeup" }],
			"55": [{ id: "99", name: "Laptops" }],
			"58": [{ id: "59", name: "Headset" }],
			"71": [{ id: "72", name: "Home Appliances" }],
			"277": [{ id: "896", name: "Fiction Books" }]
		},
		L3: {
			"37": [
				{ id: "38", name: "Face Makeup" },
				{ id: "187", name: "Makeup Accessory" },
				{ id: "329", name: "Lips" },
				{ id: "366", name: "Nails" },
				{ id: "1163", name: "Eye Makeup" },
				{ id: "1644", name: "Makeup Kits & Combo" }
			],
			"59": [
				{ id: "60", name: "Earphones" },
				{ id: "109", name: "Headphones" }
			],
			"72": [
				{ id: "73", name: "Air Conditioners" },
				{ id: "134", name: "Air Coolers" },
				{ id: "139", name: "Refrigerators" },
				{ id: "140", name: "Vacuum Cleaners" },
				{ id: "151", name: "Fans" },
				{ id: "210", name: "Water purifiers" },
				{ id: "211", name: "Irons" },
				{ id: "269", name: "Voltage Stabilizers" },
				{ id: "352", name: "Immersion Rods" },
				{ id: "393", name: "Water Geysers" },
				{ id: "434", name: "Appliance Parts & Accessories" },
				{ id: "437", name: "Washing Machines" }
			],
			"88": [
				{ id: "89", name: "Women's Ballerinas" },
				{ id: "158", name: "Women's Sports Shoes" },
				{ id: "198", name: "Women's Casual Shoes" },
				{ id: "276", name: "Women's Slippers & Flip Flops" },
				{ id: "408", name: "Women's Heels" },
				{ id: "533", name: "Women's Flats" },
				{ id: "675", name: "Women's Wedges" },
				{ id: "1125", name: "Women's Ethnic Shoes" },
				{ id: "1475", name: "Women's Sports Sandals" },
				{ id: "1900", name: "Women's Formals" }
			],
			"896": [
				{ id: "897", name: "General Fiction Books" },
				{ id: "1594", name: "Crime, Mystery and Thriller Books" },
				{ id: "1606", name: "Myths and Fairytale Books" },
				{ id: "2340", name: "Romance Books" },
				{ id: "2426", name: "Action and Adventure Books" },
				{ id: "2511", name: "Fantasy Fiction Books" },
				{ id: "2650", name: "Science Fiction Books" },
				{ id: "2690", name: "Supernatural and Horror Fiction Books" },
				{ id: "2825", name: "Religion and Spirituality Fiction Books" },
				{ id: "2882", name: "Historical Fiction Books" }
			]
		}
	};

	interface GenericNamesByCategory {
		[key: string]: string[];
	}

	const genericNamesByCategory: GenericNamesByCategory = {
		"8": ["Smartphone", "Feature Phone", "Basic Phone"], // Mobiles
		"10": ["Sneakers", "Flats", "Heels", "Sandals", "Sports Shoes", "Casual Shoes"], // Footwear
		"28": ["Analog Watch", "Digital Watch", "Smart Watch"], // Watches
		"36": ["Face Cream", "Lipstick", "Eye Shadow", "Foundation", "Nail Polish"], // Beauty
		"55": ["Gaming Laptop", "Business Laptop", "Student Laptop"], // Computers
		"58": ["Wireless Earphones", "Wired Earphones", "Over-ear Headphones", "TWS"], // Audio
		"71": ["Air Conditioner", "Refrigerator", "Washing Machine", "Water Purifier"], // Home & Kitchen
		"277": ["Fiction Book", "Mystery Book", "Romance Novel", "Fantasy Book"] // Books
	};

	const getGenericNames = (l1Id: string) => {
		return genericNamesByCategory[l1Id] || [];
	};

	const genericSuggestions = [
		"Smartphone", "Laptop", "Watch", "Headphones", "Earphones",
		"Shoes", "Sandals", "Sneakers", "Heels", "Flats",
		"Air Conditioner", "Refrigerator", "Washing Machine",
		"Book", "Novel", "Lipstick", "Foundation", "Face Cream"
	];

	let selectedGenericName = '';

	const genderOptions = ['Male', 'Women', 'Unisex'];
	const cityTierOptions = ['Tier 1', 'Tier 2', 'Tier 3'];

	let loading: boolean = false;
	let error: string | null = null;
	let success: string | null = null;

	// Toggle functions for multiple selections
	const toggleGender = (gender: string) => {
		selectedGender = gender;
	};

	const toggleCityTier = (tier: string) => {
		selectedCityTier = tier;
	};

	// Add a new image URL field
	const addImageUrl = () => {
		imageUrls = [...imageUrls, ''];
	};

	// Remove an image URL field
	const removeImageUrl = (index: number) => {
		imageUrls = imageUrls.filter((_, i) => i !== index);
	};

	// Helper function to get L2 categories based on L1 selection
	const getL2Categories = (l1Id: string) => {
		return categories.L2[l1Id] || [];
	};

	// Helper function to get L3 categories based on L2 selection
	const getL3Categories = (l2Id: string) => {
		return categories.L3[l2Id] || [];
	};

	// Helper function to check if next level has categories
	const hasL2Categories = (l1Id: string) => {
		return getL2Categories(l1Id).length > 0;
	};

	const hasL3Categories = (l2Id: string) => {
		return getL3Categories(l2Id).length > 0;
	};

	const handleSubmit = async () => {
		loading = true;
		error = null;
		success = null;

		// Validate required fields
		if (
			!selectedGenericName ||
			!selectedGender ||
			!selectedCityTier ||
			!brandName
		) {
			error = 'Please fill in all required fields.';
			loading = false;
			return;
		}

		try {
			const params = new URLSearchParams();
			params.append('cityTier', selectedCityTier);
			params.append('gender', selectedGender);
			params.append('brand', brandName);
			params.append('level1Cat', selectedL1Category);
			params.append('level2Cat', hasL2Categories(selectedL1Category) ? selectedL2Category : '-1');
			params.append('level3Cat', hasL3Categories(selectedL2Category) ? selectedL3Category : '-1');
			params.append('genericName', selectedGenericName);

			const {status, data} = await getUsersPersonas(params);

			if(status === 1){
				// Navigate to success page with submitted data and response
				const submittedData = {
					productName,
					imageUrls,
					genericName: selectedGenericName,
					brandName,
					categories: {
						l1: categories.L1.find(c => c.id === selectedL1Category)?.name || '',
						l2: getL2Categories(selectedL1Category).find(c => c.id === selectedL2Category)?.name || '',
						l3: getL3Categories(selectedL2Category).find(c => c.id === selectedL3Category)?.name || ''
					},
					gender: selectedGender,
					cityTier: selectedCityTier,
					response: data
				};
				
				goto('/dashboard/product', {
					replaceState: true,
					state: { submittedData }
				});
				return;
			}
		} catch {
			error = 'Failed to create product.';
		} finally {
			productName = '';
			imageUrls = [''];
			selectedL1Category = '';
			selectedL2Category = '';
			selectedL3Category = '';
			selectedGenericName = '';
			selectedGender = '';
			selectedCityTier = '';
			brandName = '';
		}

		loading = false;
	};

	// Add these to the script section at the top
	let showGenericSuggestions = false;
	let genericNameInput: HTMLInputElement;

	const handleGenericNameFocus = () => {
		showGenericSuggestions = true;
	};

	const handleGenericNameBlur = () => {
		// Small delay to allow click event on options to fire
		setTimeout(() => {
			showGenericSuggestions = false;
		}, 200);
	};

	const selectGenericName = (suggestion: string) => {
		selectedGenericName = suggestion;
		showGenericSuggestions = false;
		if (genericNameInput) {
			genericNameInput.focus();
		}
	};
</script>

<div class="w-full">
	<div class="flex flex-col md:flex-row md:justify-between md:items-center gap-4">
		<h2 class="flex-shrink-0 text-2xl font-bold text-slate-800">Create New Product</h2>

		<button
			class="order-last mb-8 md:mb-0 md:order-none hidden md:flex h-full w-full md:w-fit items-center justify-center rounded-md border border-transparent bg-indigo-600 px-4 py-2 font-medium text-white shadow-sm hover:bg-indigo-700 focus:ring-0 focus:outline-none disabled:opacity-50"
			disabled={loading}
			onclick={handleSubmit}
		>
			{loading ? 'Creating...' : 'Create Product'}
		</button>
	</div>

	{#if success}
		<div class="mb-4 rounded-md bg-green-100 p-3 text-green-700">{success}</div>
	{/if}
	{#if error}
		<div class="mb-4 rounded-md bg-red-100 p-3 text-red-700">{error}</div>
	{/if}

	<div class="space-y-4 mb-10">
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
								onclick={() => removeImageUrl(index)}
								class="mt-1 w-full min-w-44 rounded-md bg-red-500 px-3 py-2 text-nowrap text-white hover:bg-red-600 md:w-auto"
							>
								Remove
							</button>
						{:else}
							<button
								type="button"
								onclick={addImageUrl}
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
			<div class="flex-1 relative">
				<label for="genericName" class="block text-sm font-medium text-slate-600">Generic Name</label>
				<input
					id="genericName"
					bind:this={genericNameInput}
					type="text"
					bind:value={selectedGenericName}
					onfocus={handleGenericNameFocus}
					onblur={handleGenericNameBlur}
					placeholder="Type or select generic name"
					class="mt-1 block w-full rounded-md border-slate-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
					required
				/>
				{#if showGenericSuggestions}
					<div class="absolute z-10 w-full mt-1">
						<select
							size={Math.min(5, genericSuggestions.length)}
							class="w-full rounded-md border-slate-300 shadow-lg focus:border-indigo-500 focus:ring-indigo-500 bg-white"
							onchange={(e) => selectGenericName((e.target as HTMLSelectElement).value)}
						>
							{#each getGenericNames(selectedL1Category).length > 0 ? getGenericNames(selectedL1Category) : genericSuggestions as suggestion}
								<option value={suggestion}>{suggestion}</option>
							{/each}
						</select>
					</div>
				{/if}
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
		<div class="space-y-4 flex flex-col md:flex-row gap-4 w-full">
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
						<option value={category.id}>{category.name}</option>
					{/each}
				</select>
			</div>

			<div class="flex-1">
				<label for="l2category" class="block text-sm font-medium text-slate-600">Category L2</label>
				<select
					id="l2category"
					bind:value={selectedL2Category}
					class="mt-1 block w-full rounded-md border-slate-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 disabled:cursor-not-allowed disabled:bg-slate-100"
					required={hasL2Categories(selectedL1Category)}
					disabled={!selectedL1Category || !hasL2Categories(selectedL1Category)}
				>
					<option value="">
						{#if !selectedL1Category}
							Please select Category L1 first
						{:else if !hasL2Categories(selectedL1Category)}
							No subcategories available
						{:else}
							Select L2 Category
						{/if}
					</option>
					{#each getL2Categories(selectedL1Category) as category}
						<option value={category.id}>{category.name}</option>
					{/each}
				</select>
			</div>

			<div class="flex-1">
				<label for="l3category" class="block text-sm font-medium text-slate-600">Category L3</label>
				<select
					id="l3category"
					bind:value={selectedL3Category}
					class="mt-1 block w-full rounded-md border-slate-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500 disabled:cursor-not-allowed disabled:bg-slate-100"
					required={hasL3Categories(selectedL2Category)}
					disabled={!selectedL2Category || !hasL3Categories(selectedL2Category)}
				>
					<option value="">
						{#if !selectedL1Category}
							Please select Category L1 first
						{:else if !selectedL2Category}
							Please select Category L2 first
						{:else if !hasL3Categories(selectedL2Category)}
							No subcategories available
						{:else}
							Select L3 Category
						{/if}
					</option>
					{#each getL3Categories(selectedL2Category) as category}
						<option value={category.id}>{category.name}</option>
					{/each}
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
								type="radio"
								name="gender"
								value={gender}
								checked={selectedGender === gender}
								onchange={() => toggleGender(gender)}
								class="rounded-full border-slate-300 text-indigo-600 focus:ring-indigo-500"
							/>
							<span class="ml-2">{gender}</span>
						</label>
					{/each}
				</div>
			</div>

			<!-- City Tier Selection -->
			<div class="flex-1">
				<span class="block text-sm font-medium text-slate-700">Target Cities</span>
				<div class="mt-2 space-x-4">
					{#each cityTierOptions as tier}
						<label class="inline-flex items-center">
							<input
								type="radio"
								name="cityTier"
								value={tier}
								checked={selectedCityTier === tier}
								onchange={() => toggleCityTier(tier)}
								class="rounded-full border-slate-300 text-indigo-600 focus:ring-indigo-500"
							/>
							<span class="ml-2">{tier}</span>
						</label>
					{/each}
				</div>
			</div>
		</div>

		<button
			class="order-last mb-8 md:mb-0 md:order-none md:hidden flex h-full w-full md:w-fit items-center justify-center rounded-md border border-transparent bg-indigo-600 px-4 py-2 font-medium text-white shadow-sm hover:bg-indigo-700 focus:ring-0 focus:outline-none disabled:opacity-50"
			disabled={loading}
			onclick={handleSubmit}
		>
			{loading ? 'Creating...' : 'Create Product'}
		</button>
	</div>
</div>
