<script>
	let seed = "";
	let password = "";
	let decrypted = "";
	import iocane from "iocane/web";

	async function decrypt() {
		let decoded = await iocane.createAdapter().decrypt(seed, password);
		decrypted = decoded.split(",").join(" ");
	}
</script>

<main>
	<h1>Citadel seed decryptor</h1>
	<p>This app decrypts the seed in our browser. No data is send over to our servers.</p>
	<input placeholder='Encrypted seed without ""' bind:value={seed}>
	<input placeholder="password" bind:value={password} type="password">
	<button on:click={decrypt}>Decrypted</button>
	<p>Your seed is: {decrypted}</p>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>