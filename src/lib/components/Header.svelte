<script lang="ts">
	import logo from '$lib/images/logoFull.svg';
</script>

<header
	class="fixed top-0 right-0 left-0 z-50 border-b border-gray-200 bg-white dark:border-gray-800 dark:bg-gray-900"
>
	<div class="mx-auto max-w-[1440px] px-6 lg:px-8">
		<div
			class="flex h-16 items-center justify-between border-b border-gray-200 dark:border-gray-800"
		>
			<div class="flex items-center">
				<a href="/analytics/spend-calculator" class="flex items-center"
					><img src={logo} alt="Logo" class="h-8 w-auto" /></a
				>
			</div>
			<div class="mx-8 hidden max-w-md flex-1 md:flex">
				<div class="relative w-full">
					<input
						type="text"
						placeholder="Search..."
						class="h-10 w-full rounded-lg border border-gray-200 bg-gray-50/50 pr-4 pl-10 focus:border-transparent focus:ring-2 focus:ring-blue-500 focus:outline-none dark:border-gray-700 dark:bg-gray-800 dark:text-gray-300"
					/><svg
						xmlns="http://www.w3.org/2000/svg"
						width="24"
						height="24"
						viewBox="0 0 24 24"
						fill="none"
						stroke="currentColor"
						stroke-width="2"
						stroke-linecap="round"
						stroke-linejoin="round"
						class="lucide lucide-search absolute top-1/2 left-3 -translate-y-1/2 text-gray-400"
						><circle cx="11" cy="11" r="8"></circle><path d="m21 21-4.3-4.3"></path></svg
					>
				</div>
			</div>
			<div class="flex items-center space-x-4">
				<button
					class="rounded-lg p-2 text-gray-600 transition-colors hover:bg-gray-100 dark:text-gray-300 dark:hover:bg-gray-800"
					><svg
						xmlns="http://www.w3.org/2000/svg"
						width="24"
						height="24"
						viewBox="0 0 24 24"
						fill="none"
						stroke="currentColor"
						stroke-width="2"
						stroke-linecap="round"
						stroke-linejoin="round"
						class="lucide lucide-moon h-5 w-5"
						><path d="M12 3a6 6 0 0 0 9 9 9 9 0 1 1-9-9Z"></path></svg
					></button
				><button
					class="rounded-lg p-2 text-gray-600 hover:bg-gray-50 dark:text-gray-400 dark:hover:bg-gray-800"
					><svg
						xmlns="http://www.w3.org/2000/svg"
						width="24"
						height="24"
						viewBox="0 0 24 24"
						fill="none"
						stroke="currentColor"
						stroke-width="2"
						stroke-linecap="round"
						stroke-linejoin="round"
						class="lucide lucide-bell h-5 w-5"
						><path d="M10.268 21a2 2 0 0 0 3.464 0"></path><path
							d="M3.262 15.326A1 1 0 0 0 4 17h16a1 1 0 0 0 .74-1.673C19.41 13.956 18 12.499 18 8A6 6 0 0 0 6 8c0 4.499-1.411 5.956-2.738 7.326"
						></path></svg
					></button
				>
				<div class="h-6 w-px bg-gray-200 dark:bg-gray-700"></div>
				<div class="user-menu-container relative">
					<button
						class="flex items-center space-x-3 rounded-lg py-1.5 pr-3 pl-1 hover:bg-gray-50 dark:hover:bg-gray-800"
						><span class="relative flex shrink-0"
							><div
								class="flex h-8 w-8 items-center justify-center rounded-full bg-blue-100 dark:bg-blue-900"
							>
								<svg
									xmlns="http://www.w3.org/2000/svg"
									width="24"
									height="24"
									viewBox="0 0 24 24"
									fill="none"
									stroke="currentColor"
									stroke-width="2"
									stroke-linecap="round"
									stroke-linejoin="round"
									class="lucide lucide-user h-4 w-4 text-blue-600 dark:text-blue-400"
									><path d="M19 21v-2a4 4 0 0 0-4-4H9a4 4 0 0 0-4 4v2"></path><circle
										cx="12"
										cy="7"
										r="4"
									></circle></svg
								>
							</div>
							<span
								class="absolute right-0 bottom-0 block h-2 w-2 rounded-full bg-green-400 ring-2 ring-white dark:ring-gray-900"
							></span></span
						><span class="hidden flex-col items-start md:flex"
							><span class="text-sm font-medium text-gray-700 dark:text-gray-300"
								>rakeshrangdal@buyhatke.com</span
							><span class="text-xs text-gray-500 dark:text-gray-400">Administrator</span></span
						></button
					>
				</div>
			</div>
		</div>
		<div class="flex h-14 items-center justify-between">
			<div class="flex items-center space-x-2">
				<a
					class="flex items-center rounded-lg px-4 py-2 text-sm font-medium text-gray-600
transition-colors hover:bg-gray-50 hover:text-gray-900 dark:text-gray-400 dark:hover:bg-gray-800 dark:hover:text-gray-200"
					href="/live-stats"
					data-discover="true"
					><svg
						xmlns="http://www.w3.org/2000/svg"
						width="24"
						height="24"
						viewBox="0 0 24 24"
						fill="none"
						stroke="currentColor"
						stroke-width="2"
						stroke-linecap="round"
						stroke-linejoin="round"
						class="lucide lucide-chart-no-axes-column mr-3 h-5 w-5 text-gray-400 dark:text-gray-500"
						><line x1="18" x2="18" y1="20" y2="10"></line><line x1="12" x2="12" y1="20" y2="4"
						></line><line x1="6" x2="6" y1="20" y2="14"></line></svg
					>Spend Calculator</a
				>
			</div>
		</div>
	</div>
</header>
