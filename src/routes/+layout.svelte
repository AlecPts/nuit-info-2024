<script>
	import Test from '$lib/Test.svelte';
	import Cookie from '$lib/Cookie.svelte';
import '../app.css';
	let { children } = $props();
</script>

{@render children()}


<Cookie />
