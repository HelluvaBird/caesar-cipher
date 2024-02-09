<script lang="ts">
	let action: (() => void) | null = null;
	let shift = 0;
	let text = '';
	let buttonText = '-';
	const plaintext = 'abcdefghijklmnopqrstuvwxyz';
	$: cipher = plaintext.slice(shift) + plaintext.slice(0, shift);

	const encryptText = () => {
		if (!action || shift === 0 || text.length <= 0) return;
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
		if (!action || shift === 0 || text.length <= 0) return;
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
		if (!action || shift === 0 || text.length <= 0) return;
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

<section class="max-w-6xl w-full bg-gray-700 p-4 rounded-md">
	<div class="md:grid md:grid-cols-3 md:gap-6">
		<div class="md:col-span-1">
			<div class="">
				<h1 class="text-4xl">The Caesar Cipher</h1>
				<p class="hidden md:block mt-1 text-sm text-gray-400">
					The Caesar cipher is a single-alphabet substitution cipher. Named after Julius Caesar, the
					roman general and statesman, who used it to encrypt his private correspondence. This
					cipher is one of the simplest and most widely used encryption techniques. By todays
					standards the Caesar cipher is considered very weak and easily broken, but can still be
					found today in children's toys and obscuring text on Usenet.
				</p>
			</div>
		</div>
		<div class="mt-5 md:mt-0 md:col-span-2">
			<fieldset>
				<div>
					<legend class="text-xl font-medium">Actions</legend>
					<p class="text-sm text-gray-400">These are the actions you can perform on the text.</p>
				</div>
				<div class="mt-4 space-y-4 md:flex md:space-x-4 md:space-y-0">
					<div class="flex items-center">
						<input
							id="encrypt"
							name="cipher-action"
							type="radio"
							class="focus:ring-indigo-500 h-4 w-4 text-indigo-600 border-gray-300"
							on:change={() => {
								action = encryptText;
								buttonText = 'Encrypt';
							}}
						/>
						<label for="encrypt" class="ml-3 block text-sm font-medium">Encrypt</label>
					</div>
					<div class="flex items-center">
						<input
							id="decrypt"
							name="cipher-action"
							type="radio"
							class="focus:ring-indigo-500 h-4 w-4 text-indigo-600 border-gray-300"
							on:change={() => {
								action = decryptText;
								buttonText = 'Decrypt';
							}}
						/>
						<label for="decrypt" class="ml-3 block text-sm font-medium">Decrypt</label>
					</div>
					<div class="flex items-center">
						<input
							id="bruteforce"
							name="cipher-action"
							type="radio"
							class="focus:ring-indigo-500 h-4 w-4 text-indigo-600 border-gray-300"
							on:change={() => {
								action = bruteforce;
								buttonText = 'Bruteforce';
							}}
						/>
						<label for="bruteforce" class="ml-3 block text-sm font-medium"> Bruteforce </label>
					</div>
				</div>
			</fieldset>
			<div class="mt-4">
				<div>
					<label for="cipher" class="text-xl font-medium">Shift Cipher</label>
					<p class="text-sm text-gray-400">The number of times to shift the cipher.</p>
				</div>

				<div class="mt-4">
					<input
						type="number"
						name="cipher"
						id="cipher"
						bind:value={shift}
						min="0"
						max="25"
						class="text-gray-900 shadow-sm focus:ring-indigo-500 focus:border-indigo-500 block border-gray-300 rounded-md ps-1 w-1/2 md:w-auto"
					/>
				</div>
			</div>
			<div class="mt-4">
				<div>
					<label for="text" class="text-xl font-medium">Text</label>
					<p class="text-sm text-gray-400">the text you wish to perform the action on.</p>
				</div>

				<div class="mt-4">
					<textarea
						rows="4"
						name="text"
						id="text"
						bind:value={text}
						class="text-gray-900 shadow-sm focus:ring-indigo-500 focus:border-indigo-500 block w-full border-gray-300 rounded-md px-1"
					></textarea>
				</div>
			</div>
			<div class="mt-4 flex justify-end">
				<button
					on:click={action}
					disabled={!action}
					type="button"
					class="inline-flex items-center px-6 py-3 border border-transparent text-base font-medium rounded-md shadow-sm text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 disabled:bg-gray-400 disabled:cursor-not-allowed"
					>{buttonText}</button
				>
			</div>
		</div>
	</div>
</section>

<style lang="postcss">
	:global(html) {
		background-color: theme(colors.gray.800);
		color: theme(colors.gray.50);
	}
</style>
