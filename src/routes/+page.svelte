<script lang="ts">
	import Login from '$lib/components/Login.svelte';
	import Header from '$lib/components/Header.svelte';
	import { authStore, logout } from '$lib/stores/auth';
</script>

{#if $authStore.isAuthenticated}
	<div class="container">
		<Header />
		<main class="rounded-md bg-white p-4 shadow-md">
			<p>Welcome to the admin dashboard! You are now logged in.</p>
			<!-- Add your admin dashboard content here -->
		</main>
	</div>
{:else}
	<Login />
{/if}
