<script>
	let seed = "";
	let password = "";
	let decrypted = "";
	let userJSON = "";
	let userSeed = "";
	let newPassword = "";
	let newUserJSON = "";
	import iocane from "iocane/web";
	import { hashSync } from "./bcrypt.js";

	async function decrypt() {
		try {
			let decoded = await iocane.createAdapter().decrypt(seed, password);
			decrypted = decoded.split(",").join(" ");
		} catch {
			decrypted = "Invalid seed or password";
		}
	}
	async function encrypt() {
		seed = await iocane.createAdapter().encrypt(decrypted, password);
	}
	async function updateUserJson() {
		try {
			let parsedUserJSON = JSON.parse(userJSON);
			let newEncryptedSeed = await iocane.createAdapter().encrypt(userSeed.split(" ").join(","), newPassword);
			let newHashedPassword = hashSync(newPassword, 10);
			parsedUserJSON.seed = newEncryptedSeed;
			parsedUserJSON.password = newHashedPassword;
			newUserJSON = JSON.stringify(parsedUserJSON);
		} catch(error) {
			console.log(error);
			newUserJSON = "Invalid data!";
		}
	}

	function switchNode() {
		let codeBlocks = document.querySelectorAll("code");
		codeBlocks.forEach((codeblock) => {
			codeblock.innerText = codeblock.innerText
					.replace("umbrel", document.querySelector('input[name="node"]:checked').value)
					.replace("citadel", document.querySelector('input[name="node"]:checked').value);
		})
	}
</script>

<main>
	<h1>user.json utils</h1>
	<p>This app decrypts the seed in our browser. No data is send over to our servers.</p>
	<input placeholder='Encrypted seed without ""' bind:value={seed}>
	<input placeholder="password" bind:value={password} type="password">
	<input placeholder="Decrypted seed" bind:value={decrypted}>
	<button on:click={decrypt}>Decrypt</button>
	<button on:click={encrypt}>Encrypt</button>
	<br>
	<h1>Reset your password</h1>
	<p>Forgot your password, but still have your 24 words? Reset your password here!</p>
	<input type="radio" name="node" on:click={switchNode} value="umbrel" id="umbrel" class="inline">
	<label for="umbrel" class="inline">Umbrel</label>
	<input type="radio" name="node" on:click={switchNode} value="citadel" id="citadel" class="inline" checked="true">
	<label for="umbrel" class="inline">Citadel</label>
	<p>First, turn off your node (unplug it) and reflash the SD card. This will reset the SSH password to "moneyprintergobrrr".</p>
	<p>Then, SSH into your node and run this command:</p>
	<p><code>cat ~/citadel/db/user.json</code></p>
	<p>Then copy the output and paste it here:</p>
	<textarea placeholder='user.json' bind:value={userJSON}></textarea>
	<p>Now, enter your 24 words here (separated by spaces, all lowercase). If you don't have these anymore, no worries, but the display 24 words option on the dashboard won't work after this change again and you won't be able to get them anymore.</p>
	<input placeholder="24 words" bind:value={userSeed}>
	<p>Finally, choose a new password:</p>
	<input placeholder="New password" bind:value={newPassword} type="password">
	<p><button on:click={updateUserJson}>Update user.json</button></p>
	<p>To apply these changes, run <code>nano ~/citadel/db/user.json</code>, delete everything in the file, and put this into it:</p>
	<textarea bind:value={newUserJSON} readonly></textarea>
	<p>Then save with CTRL+X and try to log in again on the dashboard.</p>

	<footer>
		<p>Made by <a href="https://twitter.com/AaronDewes">@AaronDewes</a>.<br> Not an official Umbrel tool. This page is in no way affiliated, endorsed or supported by Umbrel.</p>
			<p>Please be careful when using this page, I'm not responsible for your node breaking when using this page. Please don't use this for illegal activity, this is designed to recover your <b>own</b> node.</p>
	</footer>
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

	textarea {
		width: 80vw;
		height: 20vh;
	}

	footer {
		text-align: center;
		padding: 1em;
		font-size: 0.8em;
		background: lightgray;
		width: 100%;
		margin: 0;
		box-sizing: border-box;
	}

	.inline {
		display: inline;
	}
</style>
