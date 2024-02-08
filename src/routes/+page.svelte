<script lang="ts">
	let decrypt = false;
	let bruteForce = false;
	let shift = 0;
	let text = '';
	const plaintext = 'abcdefghijklmnopqrstuvwxyz';
	$: cipher = plaintext.slice(shift) + plaintext.slice(0, shift);

	const encryptText = () => {
		if (text.length <= 0) return;
		let encryptedText = '';
		for (const char of text) {
			if (char.match(/[\s.,;:!?"'\-]/gi)) {
				encryptedText += char;
				continue;
			}
			const isUppercase = /[A-Z]/g;
			if (isUppercase.test(char)) {
				encryptedText += cipher.charAt(plaintext.indexOf(char.toLowerCase())).toUpperCase();
				continue;
			}
			encryptedText += cipher.charAt(plaintext.indexOf(char));
		}

		text = encryptedText;
	};

	const decryptText = () => {
		if (text.length <= 0) return;
		let decryptedText = '';
		for (const char of text) {
			if (char.match(/[\s.,;:!?"'\-]/gi)) {
				decryptedText += char;
				continue;
			}
			const isUppercase = /[A-Z]/g;
			if (isUppercase.test(char)) {
				decryptedText += plaintext.charAt(cipher.indexOf(char.toLowerCase())).toUpperCase();
				continue;
			}
			decryptedText += plaintext.charAt(cipher.indexOf(char));
		}

		text = decryptedText;
	};

	const bruteforce = () => {
		if (text.length <= 0) return;
		const originalText = text;
		let hugeText = 'original text: ' + originalText + '\n';

		for (let i = 1; i < plaintext.length; i++) {
			let bruteForceText = '';
			const cipherText = plaintext.slice(i) + plaintext.slice(0, i);
			hugeText += `shift ${i} => `;
			for (const char of originalText) {
				if (char.match(/[\s.,;:!?"'\-]/gi)) {
					bruteForceText += char;
					continue;
				}
				const isUppercase = /[A-Z]/g;
				if (isUppercase.test(char)) {
					bruteForceText += plaintext.charAt(cipherText.indexOf(char.toLowerCase())).toUpperCase();
					continue;
				}
				bruteForceText += plaintext.charAt(cipherText.indexOf(char));
			}

			hugeText += bruteForceText + '\n';
		}

		text = hugeText;
	};
</script>

<section class="min-h-96 max-w-6xl w-full bg-gray-700 p-4 rounded-md">
	<h1 class="text-center text-4xl">The Caesar Cipher</h1>
	<hr />
	<div class="space-y-5 mt-5">
		<div class="flex items-center gap-4">
			<!-- Enabled: "bg-indigo-600", Not Enabled: "bg-gray-200" -->
			<div class="flex items-center">
				<span class="mr-3" id="encryptdecrypt">
					<span class="text-sm font-medium">Encrypt</span>
				</span>
				<button
					type="button"
					on:click={() => (decrypt = !decrypt)}
					class="relative bg-indigo-600 inline-flex flex-shrink-0 h-6 w-11 border-2 border-transparent rounded-full cursor-pointer transition-colors ease-in-out duration-200 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
					role="switch"
					aria-checked={decrypt}
					aria-labelledby="encryptdecrypt"
				>
					<!-- Enabled: "translate-x-5", Not Enabled: "translate-x-0" -->
					<span
						aria-hidden="true"
						class:translate-x-0={!decrypt}
						class:translate-x-5={decrypt}
						class="pointer-events-none inline-block h-5 w-5 rounded-full bg-white shadow transform ring-0 transition ease-in-out duration-200"
					></span>
				</button>
				<span class="ml-3" id="encryptdecrypt">
					<span class="text-sm font-medium">Decrypt</span>
				</span>
			</div>
			<div
				class="flex items-center transition-opacity ease-in-out duration-200"
				class:opacity-0={!decrypt}
				class:opacity-100={decrypt}
			>
				<button
					type="button"
					on:click={() => (bruteForce = !bruteForce)}
					class:bg-gray-200={!bruteForce}
					class:bg-indigo-600={bruteForce}
					class="relative inline-flex flex-shrink-0 h-6 w-11 border-2 border-transparent rounded-full cursor-pointer transition-colors ease-in-out duration-200 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
					role="switch"
					aria-checked={bruteForce}
					aria-labelledby="bruteforceattack"
				>
					<!-- Enabled: "translate-x-5", Not Enabled: "translate-x-0" -->
					<span
						aria-hidden="true"
						class:translate-x-0={!bruteForce}
						class:translate-x-5={bruteForce}
						class="pointer-events-none inline-block h-5 w-5 rounded-full bg-white shadow transform ring-0 transition ease-in-out duration-200"
					></span>
				</button>
				<span class="ml-3" id="bruteforceattack">
					<span class="text-sm font-medium">Bruteforce</span>
				</span>
			</div>
		</div>
		<div>
			<label for="cipher" class="block text-sm font-medium">Shift Cipher</label>
			<div class="mt-1">
				<input
					type="number"
					name="cipher"
					id="cipher"
					bind:value={shift}
					min="0"
					max="25"
					class="text-gray-900 shadow-sm focus:ring-indigo-500 focus:border-indigo-500 block border-gray-300 rounded-md ps-1"
				/>
			</div>
		</div>
		<div>
			<label for="text" class="block text-sm font-medium"
				>Text to {bruteForce && decrypt ? 'bruteforce' : decrypt ? 'decrypt' : 'encrypt'}</label
			>
			<div class="mt-1">
				<textarea
					rows="4"
					name="text"
					id="text"
					bind:value={text}
					class="text-gray-900 shadow-sm focus:ring-indigo-500 focus:border-indigo-500 block w-full border-gray-300 rounded-md px-1"
				></textarea>
			</div>
		</div>
		<div class="flex justify-end">
			{#if bruteForce && decrypt}
				<button
					on:click={bruteforce}
					type="button"
					class="inline-flex items-center px-6 py-3 border border-transparent text-base font-medium rounded-md shadow-sm text-white bg-red-600 hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-red-500"
					>Bruteforce text</button
				>
			{:else}
				<button
					on:click={decrypt ? decryptText : encryptText}
					type="button"
					class="inline-flex items-center px-6 py-3 border border-transparent text-base font-medium rounded-md shadow-sm text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
					>{bruteForce && decrypt ? 'Bruteforce' : decrypt ? 'Decrypt' : 'Encrypt'} text</button
				>
			{/if}
		</div>
	</div>
</section>

<style lang="postcss">
	:global(html) {
		background-color: theme(colors.gray.800);
		color: theme(colors.gray.50);
	}
</style>
