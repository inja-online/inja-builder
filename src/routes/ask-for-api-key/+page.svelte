<script lang="ts">
	import { onMount } from 'svelte';
	import { settingsStorage } from '$lib/storage'; // Import settingsStorage
	import { goto } from '$app/navigation'; // Import goto
	import { Eye, EyeOff, Sparkles, Zap, Code, Globe, Github } from '@lucide/svelte';

	let apiKey = $state('');
	const apiKeyStorageKey = 'openrouter_api_key'; // Key for IndexedDB

	let checkingConnection = $state(false);
	let connectionStatus = $state<'idle' | 'success' | 'error'>('idle');
	let modelsError = $state('');
	let generalMessage = $state(''); // For general success/error messages not related to connection check
	let showApiKey = $state(false);

	onMount(async () => {
		const storedKey = await settingsStorage.getSetting<string>(apiKeyStorageKey);
		if (storedKey) {
			apiKey = storedKey;
			await goto('/', { replaceState: true }); // Redirect if key already exists
		}
	});

	async function saveApiKey() {
		if (apiKey.trim()) {
			try {
				await settingsStorage.setSetting(apiKeyStorageKey, apiKey.trim());
				alert('API Key saved successfully!');
				await goto('/', { replaceState: true }); // Redirect after saving
			} catch (error) {
				console.error('Failed to save API key:', error);
				alert('Failed to save API Key. See console for details.');
			}
		} else {
			alert('Please enter a valid API Key.');
		}
	}

	async function handleCheckConnectionAndSave() {
		if (!apiKey.trim()) {
			generalMessage = 'Please enter a valid API Key.';
			connectionStatus = 'error';
			return;
		}

		checkingConnection = true;
		connectionStatus = 'idle';
		modelsError = '';
		generalMessage = '';

		try {
			// Create a temporary function that uses the provided API key
			const testApiCall = async () => {
				const headers = {
					Authorization: `Bearer ${apiKey.trim()}`,
					"Content-Type": "application/json",
				};

				const body = {
					model: "qwen/qwen3-32b",
					messages: [{ role: "user", content: "Hello" }],
				};

				const response = await fetch("https://openrouter.ai/api/v1/chat/completions", {
					method: "POST",
					headers,
					body: JSON.stringify(body),
				});

				if (!response.ok) {
					const errorBody = await response.text();
					throw new Error(`HTTP error! status: ${response.status}, message: ${errorBody}`);
				}

				return await response.json();
			};

			await testApiCall();
			connectionStatus = 'success';

			// If connection is successful, then save the API key
			await settingsStorage.setSetting(apiKeyStorageKey, apiKey.trim());
			generalMessage = 'API Key is valid and saved successfully!';
			setTimeout(() => {
				goto('/', { replaceState: true }); // Redirect after saving and successful check
			}, 2000); 

		} catch (error: any) {
			console.error('Failed to check connection or save API key:', error);
			modelsError = error.message || 'An unknown error occurred while checking the connection.';
			connectionStatus = 'error';
		} finally {
			checkingConnection = false;
		}
	}
</script>

<div class="min-h-screen flex flex-col items-center justify-center bg-zinc-900 text-zinc-100 p-4">
	<div class="w-full max-w-7xl grid lg:grid-cols-2 gap-6 lg:gap-8 items-start">
		<!-- Info Section -->
		<div class="relative rounded-3xl border border-zinc-700/40 bg-gradient-to-br from-zinc-800/80 to-zinc-800/40 backdrop-blur-sm overflow-hidden">
			<div class="absolute inset-0 bg-gradient-to-br from-sky-500/5 via-transparent to-zinc-700/5 opacity-0 hover:opacity-100 transition-opacity duration-500"></div>
			<div class="relative z-10 p-6 sm:p-8">
				<div class="space-y-6 sm:space-y-8">
					<div>
						<div class="flex flex-col sm:flex-row sm:items-center gap-3 sm:gap-4 mb-4">
							<div class="rounded-2xl bg-gradient-to-br from-sky-500/20 to-zinc-600/20 p-2.5 sm:p-3 w-fit">
								<Sparkles class="h-5 w-5 sm:h-6 sm:w-6 text-sky-400" />
							</div>
							<h3 class="font-bold text-3xl sm:text-4xl text-white">Builder</h3>
						</div>
						<p class="text-zinc-400 text-base sm:text-lg mb-4 sm:mb-6 leading-relaxed">
							A website builder that converts natural language to a functional, easy to deploy website.
						</p>
						<p class="text-zinc-300 text-base sm:text-lg leading-relaxed">
							Transform your ideas into reality with our AI-powered website builder. Simply describe what you want, and watch as our system generates clean, modern HTML code in real-time.
						</p>
					</div>

					<!-- Video Embed -->
					<div class="rounded-2xl overflow-hidden border border-zinc-700/50">
						<div class="relative" style="padding-bottom: 56.25%;">
							<iframe
								class="absolute inset-0 w-full h-full"
								src="https://www.youtube.com/embed/XMm-aPl9zYQ"
								title="Builder Demo Video"
								frameborder="0"
								allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
								allowfullscreen
							></iframe>
						</div>
					</div>

					<div>
						<h4 class="font-semibold text-lg sm:text-xl mb-3 sm:mb-4 text-white">Key Features</h4>
						<div class="grid grid-cols-1 gap-2.5 sm:gap-3">
							<div class="flex items-center gap-3 rounded-xl border border-zinc-700/30 bg-gradient-to-r from-zinc-700/50 to-zinc-700/30 p-2.5 sm:p-3 hover:border-sky-500/50 transition-all duration-300">
								<div class="w-2 h-2 bg-sky-400 rounded-full flex-shrink-0"></div>
								<span class="font-medium text-sm sm:text-base text-zinc-300">Natural Language to Code</span>
							</div>
							<div class="flex items-center gap-3 rounded-xl border border-zinc-700/30 bg-gradient-to-r from-zinc-700/50 to-zinc-700/30 p-2.5 sm:p-3 hover:border-sky-500/50 transition-all duration-300">
								<div class="w-2 h-2 bg-sky-400 rounded-full flex-shrink-0"></div>
								<span class="font-medium text-sm sm:text-base text-zinc-300">Real-time Preview</span>
							</div>
							<div class="flex items-center gap-3 rounded-xl border border-zinc-700/30 bg-gradient-to-r from-zinc-700/50 to-zinc-700/30 p-2.5 sm:p-3 hover:border-sky-500/50 transition-all duration-300">
								<div class="w-2 h-2 bg-sky-400 rounded-full flex-shrink-0"></div>
								<span class="font-medium text-sm sm:text-base text-zinc-300">Chat-based Interface</span>
							</div>
							<div class="flex items-center gap-3 rounded-xl border border-zinc-700/30 bg-gradient-to-r from-zinc-700/50 to-zinc-700/30 p-2.5 sm:p-3 hover:border-sky-500/50 transition-all duration-300">
								<div class="w-2 h-2 bg-sky-400 rounded-full flex-shrink-0"></div>
								<span class="font-medium text-sm sm:text-base text-zinc-300">Modern Tech Stack</span>
							</div>
							<div class="flex items-center gap-3 rounded-xl border border-zinc-700/30 bg-gradient-to-r from-zinc-700/50 to-zinc-700/30 p-2.5 sm:p-3 hover:border-sky-500/50 transition-all duration-300">
								<div class="w-2 h-2 bg-sky-400 rounded-full flex-shrink-0"></div>
								<span class="font-medium text-sm sm:text-base text-zinc-300">Instant Deployment</span>
							</div>
						</div>
					</div>

					<div>
						<h4 class="font-semibold text-lg sm:text-xl mb-4 sm:mb-6 text-white">Project Highlights</h4>
						<div class="space-y-3 sm:space-y-4">
							<div class="group relative overflow-hidden rounded-2xl border border-zinc-700/30 bg-gradient-to-r from-zinc-700/50 to-zinc-700/30 p-4 sm:p-6 hover:border-sky-500/50 transition-all duration-300">
								<div class="flex items-start gap-3 sm:gap-4">
									<div class="rounded-xl bg-gradient-to-br from-sky-500/20 to-zinc-600/20 p-2.5 sm:p-3 group-hover:scale-110 transition-transform duration-300 flex-shrink-0">
										<Zap class="h-5 w-5 sm:h-6 sm:w-6 text-sky-400" />
									</div>
									<div class="min-w-0">
										<div class="font-bold text-base sm:text-lg mb-1 text-white">2000+ tokens/second</div>
										<div class="text-zinc-400 text-sm sm:text-base">Lightning-fast generation</div>
									</div>
								</div>
							</div>
							<div class="group relative overflow-hidden rounded-2xl border border-zinc-700/30 bg-gradient-to-r from-zinc-700/50 to-zinc-700/30 p-4 sm:p-6 hover:border-sky-500/50 transition-all duration-300">
								<div class="flex items-start gap-3 sm:gap-4">
									<div class="rounded-xl bg-gradient-to-br from-sky-500/20 to-zinc-600/20 p-2.5 sm:p-3 group-hover:scale-110 transition-transform duration-300 flex-shrink-0">
										<Code class="h-5 w-5 sm:h-6 sm:w-6 text-sky-400" />
									</div>
									<div class="min-w-0">
										<div class="font-bold text-base sm:text-lg mb-1 text-white">Clean HTML Output</div>
										<div class="text-zinc-400 text-sm sm:text-base">Production-ready code</div>
									</div>
								</div>
							</div>
							<div class="group relative overflow-hidden rounded-2xl border border-zinc-700/30 bg-gradient-to-r from-zinc-700/50 to-zinc-700/30 p-4 sm:p-6 hover:border-sky-500/50 transition-all duration-300">
								<div class="flex items-start gap-3 sm:gap-4">
									<div class="rounded-xl bg-gradient-to-br from-sky-500/20 to-zinc-600/20 p-2.5 sm:p-3 group-hover:scale-110 transition-transform duration-300 flex-shrink-0">
										<Globe class="h-5 w-5 sm:h-6 sm:w-6 text-sky-400" />
									</div>
									<div class="min-w-0">
										<div class="font-bold text-base sm:text-lg mb-1 text-white">Responsive Design</div>
										<div class="text-zinc-400 text-sm sm:text-base">Mobile-first approach</div>
									</div>
								</div>
							</div>
						</div>
					</div>

					<div>
						<h4 class="font-semibold text-lg sm:text-xl mb-4 sm:mb-6 text-white">Tech Stack</h4>
						<div class="grid grid-cols-2 gap-2.5 sm:gap-3">
							<div class="group relative overflow-hidden rounded-xl border border-zinc-700/30 bg-gradient-to-r from-zinc-700/50 to-zinc-700/30 p-3 sm:p-4 hover:border-sky-500/50 transition-all duration-300">
								<div class="absolute inset-0 bg-gradient-to-r from-orange-400 to-red-500 opacity-0 group-hover:opacity-10 transition-opacity duration-300"></div>
								<span class="relative z-10 font-semibold text-sm sm:text-base text-zinc-300 group-hover:text-white transition-colors duration-300">SvelteKit</span>
							</div>
							<div class="group relative overflow-hidden rounded-xl border border-zinc-700/30 bg-gradient-to-r from-zinc-700/50 to-zinc-700/30 p-3 sm:p-4 hover:border-sky-500/50 transition-all duration-300">
								<div class="absolute inset-0 bg-gradient-to-r from-blue-400 to-purple-600 opacity-0 group-hover:opacity-10 transition-opacity duration-300"></div>
								<span class="relative z-10 font-semibold text-sm sm:text-base text-zinc-300 group-hover:text-white transition-colors duration-300">Cerebras API</span>
							</div>
							<div class="group relative overflow-hidden rounded-xl border border-zinc-700/30 bg-gradient-to-r from-zinc-700/50 to-zinc-700/30 p-3 sm:p-4 hover:border-sky-500/50 transition-all duration-300">
								<div class="absolute inset-0 bg-gradient-to-r from-green-400 to-teal-500 opacity-0 group-hover:opacity-10 transition-opacity duration-300"></div>
								<span class="relative z-10 font-semibold text-sm sm:text-base text-zinc-300 group-hover:text-white transition-colors duration-300">Alpine.js</span>
							</div>
							<div class="group relative overflow-hidden rounded-xl border border-zinc-700/30 bg-gradient-to-r from-zinc-700/50 to-zinc-700/30 p-3 sm:p-4 hover:border-sky-500/50 transition-all duration-300">
								<div class="absolute inset-0 bg-gradient-to-r from-cyan-400 to-blue-500 opacity-0 group-hover:opacity-10 transition-opacity duration-300"></div>
								<span class="relative z-10 font-semibold text-sm sm:text-base text-zinc-300 group-hover:text-white transition-colors duration-300">Tailwind CSS</span>
							</div>
						</div>
					</div>

					<div class="flex flex-col sm:flex-row gap-3 sm:gap-4 w-full pt-4 border-t border-zinc-700/30">
						<a
							target="_blank"
							rel="noopener noreferrer"
							class="group inline-flex items-center justify-center gap-2 sm:gap-3 rounded-2xl bg-sky-600 px-6 sm:px-8 py-3 sm:py-4 text-white font-semibold text-base sm:text-lg transition-all duration-300 hover:bg-sky-500 hover:scale-105 hover:shadow-xl"
							href="https://builder.inja.online"
						>
							<Globe class="h-4 w-4 sm:h-5 sm:w-5 transition-transform duration-300 group-hover:rotate-12" />
							Live Demo
						</a>
						<a
							target="_blank"
							rel="noopener noreferrer"
							class="group inline-flex items-center justify-center gap-2 sm:gap-3 rounded-2xl border-2 border-zinc-700 bg-zinc-800 px-6 sm:px-8 py-3 sm:py-4 font-semibold text-base sm:text-lg transition-all duration-300 hover:bg-zinc-700 hover:border-sky-500/50 hover:scale-105"
							href="https://github.com/inja-online/cerebras-hacketon-web-builder"
						>
							<Github class="h-4 w-4 sm:h-5 sm:w-5 transition-transform duration-300 group-hover:rotate-12" />
							View Code
						</a>
					</div>
				</div>
			</div>
		</div>

		<!-- API Key Form -->
		<div class="bg-zinc-800 p-6 sm:p-8 rounded-lg shadow-xl w-full space-y-8 lg:sticky lg:top-4">
			<header class="text-center">
				<h1 class="text-2xl sm:text-3xl font-semibold text-white">Welcome & API Key Setup</h1>
			</header>

		<section class="space-y-6">
			<p class="text-sm sm:text-base text-zinc-300 text-center">
				This is a mock website builder that uses Large Language Models (LLMs) to help you create web pages.
				To power these AI features, specifically the Cerebras Qwen models, an API key from
				<a
					href="https://openrouter.ai/"
					target="_blank"
					rel="noopener noreferrer"
					class="text-sky-400 hover:text-sky-300 underline"
				>
					OpenRouter.ai
				</a>
				is required.
			</p>
			<p class="text-sm text-zinc-400 text-center">
				Please visit their website, obtain your API key, and enter it below.
			</p>
		</section>

		<form onsubmit={(e) => { e.preventDefault(); if (apiKey.trim()) handleCheckConnectionAndSave(); }} class="space-y-8">
			<div>
				<label for="apiKey" class="block text-sm font-medium text-zinc-300 mb-2">
					OpenRouter API Key
				</label>
				<div class="relative">
					<input
						type={showApiKey ? "text" : "password"}
						id="apiKey"
						name="apiKey"
						bind:value={apiKey}
						placeholder="sk-or-..."
						class="w-full px-4 py-2.5 pr-12 bg-zinc-700 border border-zinc-600 rounded-md focus:ring-2 focus:ring-sky-500 focus:border-sky-500 outline-none transition-colors duration-200 text-zinc-100 placeholder-zinc-400"
						required
					/>
					<button
						type="button"
						onclick={() => showApiKey = !showApiKey}
						class="absolute right-3 top-1/2 transform -translate-y-1/2 text-zinc-400 hover:text-zinc-300 transition-colors duration-200"
						title={showApiKey ? "Hide API key" : "Show API key"}
					>
						{#if showApiKey}
							<EyeOff size={18} />
						{:else}
							<Eye size={18} />
						{/if}
					</button>
				</div>
			</div>

			{#if generalMessage}
				<p class="text-sm text-center px-2 py-2 rounded-md" 
				   class:text-green-300={connectionStatus === 'success' || generalMessage.includes('success')}
				   class:bg-green-900={connectionStatus === 'success' || generalMessage.includes('success')}
				   class:border-green-700={connectionStatus === 'success' || generalMessage.includes('success')}
				   class:text-red-300={connectionStatus === 'error' || !generalMessage.includes('success')}
				   class:bg-red-900={connectionStatus === 'error' || !generalMessage.includes('success')}
				   class:border-red-700={connectionStatus === 'error' || !generalMessage.includes('success')}
				>
					{generalMessage}
				</p>
			{/if}

			{#if checkingConnection && connectionStatus === 'idle'}
				<div class="bg-zinc-800 border border-zinc-700 rounded-lg p-4">
					<p class="text-zinc-300 text-sm animate-pulse text-center">Checking connection to OpenRouter...</p>
				</div>
			{:else if connectionStatus === 'success' && !generalMessage.includes('success')}
				<div class="bg-green-900/50 border border-green-700 rounded-lg p-4">
					<p class="text-green-300 text-sm font-medium text-center">Connection to OpenRouter is successful!</p>
				</div>
			{:else if connectionStatus === 'error' && modelsError}
				<div class="bg-red-900/50 border border-red-700 rounded-lg p-4">
					<p class="text-red-300 text-sm font-medium text-center">Connection Failed</p>
					<p class="text-red-300 text-xs mt-1 text-center">{modelsError}</p>
				</div>
			{/if}

			<button
				type="submit" 
				class="w-full px-4 py-2.5 bg-sky-600 hover:bg-sky-500 text-white font-medium rounded-md focus:outline-none focus:ring-2 focus:ring-sky-500 focus:ring-offset-2 focus:ring-offset-zinc-800 transition-colors duration-200 disabled:bg-zinc-700 disabled:text-zinc-400 disabled:cursor-not-allowed"
				disabled={checkingConnection || !apiKey.trim()}
			>
				{checkingConnection ? 'Checking & Saving...' : 'Check Connection & Save API Key'}
			</button>
		</form>
		</div>
	</div>

	<p class="text-xs sm:text-sm text-zinc-500 text-center pt-4 max-w-7xl mx-auto px-4">
		Important: This application stores everything, including your API key and chat history,
		locally in your browser. There is no server-side storage or syncing of your data.
	</p>
</div>