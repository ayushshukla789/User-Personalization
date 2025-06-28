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

	const validGenericNames = [
		"mobile", "laptop", "headphone", "speaker", "charger", "power bank", "smartwatch", "earbud", "tablet", "television",
		"camera", "printer", "keyboard", "mouse", "monitor", "router", "webcam", "microphone", "usb drive", "hard drive",
		"ssd", "gaming console", "gaming accessory", "drone", "smart home device", "smart light", "smart plug", "doorbell",
		"security camera", "home theatre", "projector", "blender", "mixer grinder", "pressure cooker", "induction cooktop",
		"air fryer", "microwave", "refrigerator", "washing machine", "dishwasher", "vacuum cleaner", "iron", "water purifier",
		"geyser", "fan", "air cooler", "heater", "coffee maker", "toaster", "electric kettle", "juicer", "food processor",
		"cookware set", "frying pan", "saucepan", "cooker", "kitchen utensil", "dinner set", "glassware", "storage container",
		"lunch box", "water bottle", "thermos", "cutting board", "knife set", "peeler", "grater", "apparel", "t-shirt",
		"shirt", "jeans", "trouser", "dress", "saree", "kurti", "jacket", "sweater", "hoodie", "shorts", "skirt", "top",
		"sportswear", "swimwear", "underwear", "socks", "footwear", "shoes", "sneaker", "sandal", "slipper", "heels",
		"boot", "watch", "handbag", "backpack", "wallet", "belt", "sunglasses", "jewelry set", "earrings", "necklace",
		"bracelet", "ring", "perfume", "deodorant", "shampoo", "conditioner", "soap", "body wash", "lotion", "moisturizer",
		"face wash", "serum", "sunscreen", "makeup", "lipstick", "foundation", "mascara", "eyeliner", "nail polish",
		"hair dryer", "straightener", "curler", "trimmer", "shaver", "toothbrush", "toothpaste", "mouthwash", "hand sanitizer",
		"mask", "vitamins", "supplements", "protein powder", "ayurvedic product", "medical device", "thermometer", "bp monitor",
		"pulse oximeter", "first aid kit", "yoga mat", "dumbbell", "resistance band", "treadmill", "exercise bike",
		"fitness tracker", "sports shoe", "cricket bat", "football", "basketball", "badminton racket", "tennis racket",
		"sleeping bag", "tent", "camping gear", "gardening tool", "plant seed", "pot", "fertilizer", "pest control",
		"home decor", "curtain", "bedsheet", "cushion cover", "rug", "carpet", "photo frame", "wall art", "vase", "candle",
		"decorative lamp", "figurine", "artificial plant", "furniture", "sofa", "bed", "mattress", "dining table", "chair",
		"wardrobe", "bookshelf", "study table", "tv unit", "shoe rack", "book", "fiction book", "non-fiction book",
		"children's book", "textbook", "ebook reader", "dvd", "blu-ray", "cd", "vinyl record", "video game", "toy", "doll",
		"action figure", "building block", "board game", "puzzle", "remote control toy", "stuffed animal", "baby product",
		"diaper", "baby wipes", "baby food", "baby lotion", "baby shampoo", "baby soap", "pram", "stroller", "car seat",
		"crib", "baby carrier", "pet food", "pet toy", "pet grooming tool", "pet bed", "cat litter", "dog collar",
		"dog leash", "groceries", "staples", "snack", "beverage", "breakfast cereal", "cooking oil", "spices", "flour",
		"rice", "lentils", "sugar", "salt", "tea", "coffee", "chocolate", "biscuit", "chips", "noodle", "sauce", "jam",
		"honey", "dry fruit", "nut", "canned food", "frozen food", "cleaning supply", "detergent", "floor cleaner",
		"dishwashing liquid", "toilet cleaner", "glass cleaner", "brush", "mop", "dustbin", "tissue paper", "toilet paper",
		"paper towel", "stationery", "pen", "pencil", "notebook", "paper", "eraser", "sharpener", "scale", "calculator",
		"bag", "school bag", "office supply", "file folder", "stapler", "punch machine", "glue", "tape", "art supply",
		"paint", "brush set", "canvas", "drawing book", "craft kit", "musical instrument", "guitar", "drum set", "ukulele",
		"violin", "home improvement", "drill machine", "tool kit", "screwdriver", "hammer", "wrench", "adhesive",
		"lighting fixture", "bulb", "battery", "extension cord", "power strip", "vehicle accessory", "car charger",
		"car air freshener", "car cleaner", "bike helmet", "bike accessory", "luggage", "travel bag", "suitcase",
		"duffel bag", "travel pillow", "travel adapter", "gift set", "flower", "chocolate box", "greeting card", "voucher",
		"subscription", "software", "antivirus", "operating system", "utility software", "education course", "online class",
		"web hosting", "domain name", "digital product", "ebook", "music download", "movie download", "game download",
		"gift card", "furniture cover", "car cover", "bike cover", "umbrella", "raincoat", "sleeping mask", "earplugs",
		"travel neck pillow", "electric blanket", "heating pad", "massager", "humidifier", "dehumidifier", "air purifier",
		"air conditioner", "water dispenser", "cooler", "vacuum flask", "storage box", "basket", "hanger", "clothes rack",
		"laundry basket", "drying rack", "mirror", "clock", "photo album", "journal", "diary", "calendar", "planner",
		"desk organizer", "pen stand", "file cabinet", "shredder", "projector screen", "webcam cover", "screen protector",
		"laptop stand", "mobile stand", "ring light", "tripod", "microphone stand", "keyboard cover", "mouse pad",
		"webcam light", "usb hub", "cable organizer", "cable protector", "power adapter", "travel mug", "tumbler",
		"coaster", "tablecloth", "napkin", "placemat", "cutlery set", "chopsticks", "straw", "bottle opener", "can opener",
		"kitchen scale", "timer", "oven mitt", "apron", "dish towel", "cleaning cloth", "sponge", "scrubber", "broom",
		"dustpan", "wiper", "gloves", "disinfectant", "air freshener", "insect repellent", "candle holder",
		"incense stick", "aroma diffuser", "essential oil", "massage oil", "bath bomb", "bath salt", "loofah", "hair brush",
		"comb", "razor", "shaving cream", "aftershave", "hand cream", "foot cream", "lip balm", "body butter", "face mask",
		"hair mask", "sun hat", "scarf", "belt buckle", "tie", "cufflinks", "brooch", "keychain", "swimming goggles",
		"swim cap", "gym bag", "sports water bottle", "protein bar", "energy drink", "health supplement",
		"protein supplement", "fish oil", "multivitamin", "calcium supplement", "iron supplement", "zinc supplement",
		"vitamin c", "vitamin d", "b-complex", "probiotic", "prebiotic", "digestive enzyme", "sleep aid", "stress relief",
		"pain relief", "cold medicine", "fever medicine", "allergy medicine", "antiseptic", "bandage", "gauze",
		"cotton ball", "ointment", "spray", "hand wash", "sanitizer refill", "tissue box", "face towel", "bath towel",
		"hand towel", "door mat", "shoe mat", "car mat", "boot mat", "dash cam", "car vacuum cleaner", "tire inflator",
		"jump starter", "car perfume", "car polish", "car wax", "bike lock", "bike pump", "bike light", "cycle bell",
		"cycle stand"
	];

	const validBrandNames = [
		"samsung", "apple", "oneplus", "xiaomi", "redmi", "mi", "iqoo", "realme", "vivo", "oppo",
		"motorola", "nokia", "lava", "infinix", "google", "nothing", "sony", "lg", "panasonic", "philips",
		"bajaj", "usha", "havells", "v-guard", "orient", "crompton", "bosch", "ifb", "whirlpool", "haier",
		"godrej", "kent", "eureka forbes", "prestige", "pigeon", "milton", "cello", "butterfly", "lifelong", "bajaj vacco",
		"fire-boltt", "noise", "boat", "portronics", "ambrane", "zebronics", "jbl", "anker", "soundcore", "skullcandy",
		"logitech", "hp", "dell", "asus", "lenovo", "acer", "msi", "apple", "amazon basics", "sony playstation",
		"wrogn", "puma", "adidas", "nike", "reebok", "sparx", "red tape", "bata", "campus", "asian",
		"liberty", "crocs", "woodland", "metro", "fila", "skechers", "hrx", "levis", "peter england", "allen solly",
		"van heusen", "roadster", "flying machine", "us polo", "tommy hilfiger", "dennis lingo", "bewakoof", "zara", "h&m", "max",
		"shining diva", "yellow chimes", "jpearls", "sukkhi", "aristocrat", "wildcraft", "american tourister", "skybags", "safari", "fastrack",
		"timex", "casio", "sonata", "titan", "noise", "fire-boltt", "apple watch", "boat watch", "gshock", "fitbit",
		"boldfit", "mivi", "realme buds", "anker", "intex", "micromax", "karbonn", "blaupunkt", "vu", "tcl"
	];

	let filteredGenericNames: string[] = [...validGenericNames].slice(0, 10);
	let filteredBrandNames: string[] = [...validBrandNames].slice(0, 10);
	let showGenericSuggestions = false;
	let showBrandSuggestions = false;
	let genericNameInput: HTMLInputElement;
	let brandNameInput: HTMLInputElement;

	const handleGenericNameInput = (event: Event) => {
		const input = (event.target as HTMLInputElement).value.toLowerCase();
		if (input.length === 0) {
			filteredGenericNames = validGenericNames.slice(0, 10);
		} else {
			filteredGenericNames = validGenericNames.filter(name => 
				name.toLowerCase().includes(input)
			).slice(0, 10);
		}
		showGenericSuggestions = true;
	};

	const handleBrandNameInput = (event: Event) => {
		const input = (event.target as HTMLInputElement).value.toLowerCase();
		if (input.length === 0) {
			filteredBrandNames = validBrandNames.slice(0, 10);
		} else {
			filteredBrandNames = validBrandNames.filter(name => 
				name.toLowerCase().includes(input)
			).slice(0, 10);
		}
		showBrandSuggestions = true;
	};

	const handleGenericNameFocus = () => {
		filteredGenericNames = validGenericNames.slice(0, 10);
		showGenericSuggestions = true;
	};

	const handleBrandNameFocus = () => {
		filteredBrandNames = validBrandNames.slice(0, 10);
		showBrandSuggestions = true;
	};

	const handleGenericNameBlur = () => {
		setTimeout(() => {
			showGenericSuggestions = false;
		}, 200);
	};

	const handleBrandNameBlur = () => {
		setTimeout(() => {
			showBrandSuggestions = false;
		}, 200);
	};

	const selectGenericName = (suggestion: string) => {
		selectedGenericName = suggestion;
		showGenericSuggestions = false;
		if (genericNameInput) {
			genericNameInput.value = suggestion;
			genericNameInput.focus();
		}
	};

	const selectBrandName = (suggestion: string) => {
		brandName = suggestion;
		showBrandSuggestions = false;
		if (brandNameInput) {
			brandNameInput.value = suggestion;
			brandNameInput.focus();
		}
	};

	const genderOptions = ['Male', 'Women', 'Unisex'];
	const cityTierOptions = ['Tier 1', 'Tier 2', 'Tier 3', 'No Tier'];

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

		// Validate generic name is in the allowed list
		if (!validGenericNames.includes(selectedGenericName.toLowerCase())) {
			error = 'Please select a valid generic name from the suggestions.';
			loading = false;
			return;
		}

		// Validate brand name is in the allowed list
		if (!validBrandNames.includes(brandName.toLowerCase())) {
			error = 'Please select a valid brand name from the suggestions.';
			loading = false;
			return;
		}

		try {
			const params = new URLSearchParams();
			params.append('cityTier', selectedCityTier === 'No Tier' ? '' : selectedCityTier);
			params.append('gender', selectedGender);
			params.append('brand', brandName.toLowerCase());
			params.append('level1Cat', selectedL1Category);
			params.append('level2Cat', hasL2Categories(selectedL1Category) ? selectedL2Category : '-1');
			params.append('level3Cat', hasL3Categories(selectedL2Category) ? selectedL3Category : '-1');
			params.append('genericName', selectedGenericName.toLowerCase());

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
		} 
		// finally {
		// 	productName = '';
		// 	imageUrls = [''];
		// 	selectedL1Category = '';
		// 	selectedL2Category = '';
		// 	selectedL3Category = '';
		// 	selectedGenericName = '';
		// 	selectedGender = '';
		// 	selectedCityTier = '';
		// 	brandName = '';
		// }

		loading = false;
	};

	// Add these to the script section at the top
	let selectedGenericName = '';
</script>

<div class="w-full">
	<div class="flex flex-col md:flex-row md:justify-between md:items-center gap-4">
		<h2 class="flex-shrink-0 text-2xl font-bold text-slate-800">Add New Product</h2>

		<button
			class="order-last mb-8 md:mb-0 md:order-none hidden md:flex h-full w-full md:w-fit items-center justify-center rounded-md border border-transparent bg-indigo-600 px-4 py-2 font-medium text-white shadow-sm hover:bg-indigo-700 focus:ring-0 focus:outline-none disabled:opacity-50 min-w-44"
			disabled={loading}
			onclick={handleSubmit}
		>
			{loading ? 'Creating...' : 'Create Product'}
		</button>
	</div>

	{#if success}
		<div class="my-4 rounded-md bg-green-100 p-3 text-green-700">{success}</div>
	{/if}
	{#if error}
		<div class="my-4 rounded-md bg-red-100 p-3 text-red-700">{error}</div>
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
					oninput={handleGenericNameInput}
					placeholder="Type or select generic name"
					class="mt-1 block w-full rounded-md border-slate-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
					required
				/>
				{#if showGenericSuggestions && filteredGenericNames.length > 0}
					<div class="absolute z-10 w-full mt-1 bg-white border border-gray-300 rounded-md shadow-lg max-h-60 overflow-auto">
						<ul class="py-1">
							{#each filteredGenericNames as suggestion}
								<li 
									class="px-4 py-2 hover:bg-indigo-50 cursor-pointer text-sm"
									onmousedown={() => selectGenericName(suggestion)}
								>
									{suggestion}
								</li>
							{/each}
						</ul>
					</div>
				{/if}
			</div>

			<!-- Brand Name -->
			<div class="flex-1 relative">
				<label for="brandName" class="block text-sm font-medium text-slate-600">Brand Name</label>
				<input
					type="text"
					id="brandName"
					bind:this={brandNameInput}
					bind:value={brandName}
					onfocus={handleBrandNameFocus}
					onblur={handleBrandNameBlur}
					oninput={handleBrandNameInput}
					placeholder="Type or select brand name"
					class="mt-1 block w-full rounded-md border-slate-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
					required
				/>
				{#if showBrandSuggestions && filteredBrandNames.length > 0}
					<div class="absolute z-10 w-full mt-1 bg-white border border-gray-300 rounded-md shadow-lg max-h-60 overflow-auto">
						<ul class="py-1">
							{#each filteredBrandNames as suggestion}
								<li 
									class="px-4 py-2 hover:bg-indigo-50 cursor-pointer text-sm"
									onmousedown={() => selectBrandName(suggestion)}
								>
									{suggestion}
								</li>
							{/each}
						</ul>
					</div>
				{/if}
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
