<script lang="ts">
	import '../app.css';
	let { children } = $props();
</script>

<svelte:head>
	<title>Builder - AI-Powered Website Builder | Transform Ideas into Websites</title>
	<meta name="description" content="Transform your ideas into reality with our AI-powered website builder. Simply describe what you want, and watch as our system generates clean, modern HTML code in real-time using Cerebras Qwen models." />
	<meta name="keywords" content="AI website builder, natural language to code, website generator, AI-powered, Cerebras, Qwen, SvelteKit, HTML generator, web development" />
	
	<!-- Open Graph / Facebook -->
	<meta property="og:type" content="website" />
	<meta property="og:url" content="https://builder.inja.online/" />
	<meta property="og:title" content="Builder - AI-Powered Website Builder" />
	<meta property="og:description" content="Transform your ideas into reality with our AI-powered website builder. Generate clean, modern HTML code in real-time with natural language." />
	<meta property="og:image" content="https://builder.inja.online/fav.png" />
	
	<!-- Twitter -->
	<meta property="twitter:card" content="summary_large_image" />
	<meta property="twitter:url" content="https://builder.inja.online/" />
	<meta property="twitter:title" content="Builder - AI-Powered Website Builder" />
	<meta property="twitter:description" content="Transform your ideas into reality with our AI-powered website builder. Generate clean, modern HTML code in real-time with natural language." />
	<meta property="twitter:image" content="https://builder.inja.online/fav.png" />
	
	<!-- Favicon -->
	<link rel="icon" type="image/png" href="/fav.png" />
</svelte:head>

<div class="relative min-h-screen">
	{@render children()}
</div>
