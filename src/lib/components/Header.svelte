<script lang="ts">
	import logo from '$lib/images/logoFull.svg';
	import searchIcon from '$lib/images/search.svg';
	import personIcon from '$lib/images/person.svg';
	import { authStore } from '$lib/stores/auth';
</script>

<header class="fixed top-0 right-0 left-0 z-50 border-b border-slate-200 bg-white">
	<div class="mx-auto max-w-[1440px] px-6 lg:px-8">
		<div class="flex h-16 items-center justify-between border-b border-slate-200">
			<div class="flex items-center">
				<a href="/dashboard" class="flex items-center"
					><img src={logo} alt="Logo" class="h-8 w-auto" /></a
				>
			</div>
			<div class="mx-8 hidden max-w-md flex-1 md:flex">
				<div
					class="flex h-10 w-full items-center rounded-lg border border-slate-200 bg-slate-50/50 focus-within:ring-2 focus-within:ring-indigo-600"
				>
					<img src={searchIcon} alt="search icon" class="ml-3 h-6 w-6 opacity-50" />
					<input
						type="text"
						placeholder="Search..."
						class="h-full flex-1 border-none bg-transparent px-3 focus:ring-0 focus:outline-none"
					/>
				</div>
			</div>
			<div class="flex items-center space-x-4">
				<div class="user-menu-container relative">
					<button class="flex items-center space-x-3 rounded-lg py-1.5 pr-3 pl-1 hover:bg-slate-50"
						><span class="relative flex shrink-0"
							><div class="flex h-8 w-8 items-center justify-center rounded-full bg-indigo-100">
								<img src={personIcon} alt="person Icon" class="h-4 w-4 text-indigo-600" />
							</div>
							<span
								class="absolute right-0 bottom-0 block h-2 w-2 rounded-full bg-green-400 ring-2 ring-white"
							></span></span
						><span class="hidden flex-col items-start md:flex"
							><span class="text-sm font-medium text-slate-600">{$authStore.username}</span><span
								class="text-xs text-gray-500 dark:text-gray-400">Administrator</span
							></span
						></button
					>
				</div>
			</div>
		</div>
	</div>
</header>
