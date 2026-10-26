<script>
	let metode = $state('Cesar');
	let vigenereNokkel = $state('');
	let tekst = $state('');
	let nokkel = $state(0);
	let resultat = $state('');
	let feilmelding = $state('');
	const storeBokstaver = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ';
	const smaBokstaver = 'abcdefghijklmnopqrstuvwxyz';

	function erBokstav(tegn) {
		return (tegn >= 'A' && tegn <= 'Z') || (tegn >= 'a' && tegn <= 'z');
	}

	function proverInput() {
		feilmelding = '';

		if (tekst.trim() === '') {
			feilmelding = 'Teksten kan ikke være tom.';
			return false;
		}

		if (metode === 'Vigenère') {
			if (vigenereNokkel.trim() === '') {
				feilmelding = 'Nøkkelen kan ikke være tom.';
				return false;
			}

			if (!/^[A-Za-z]+$/.test(vigenereNokkel.trim())) {
				feilmelding = 'Nøkkelen kan bare inneholde bokstaver.';
				return false;
			}
		} else {
			if (!Number.isInteger(Number(nokkel)) || nokkel < 0 || nokkel > 25) {
				feilmelding = 'Nøkkelen må være et heltall fra 0 til 25.';
				return false;
			}
		}

		for (const tegn of tekst) {
			if (tegn !== ' ' && !erBokstav(tegn)) {
				feilmelding = 'Teksten kan bare inneholde bokstaver og mellomrom.';
				return false;
			}
		}

		return true;
	}

	function krypterMedCesar() {
		resultat = '';

		for (const tegn of tekst) {
			if (tegn === ' ') {
				resultat += ' ';
				continue;
			}

			if (storeBokstaver.includes(tegn)) {
				const posisjon = storeBokstaver.indexOf(tegn);
				const nyPosisjon = (posisjon + Number(nokkel)) % 26;
				resultat += storeBokstaver[nyPosisjon];
			}

			if (smaBokstaver.includes(tegn)) {
				const posisjon = smaBokstaver.indexOf(tegn);
				const nyPosisjon = (posisjon + Number(nokkel)) % 26;
				resultat += smaBokstaver[nyPosisjon];
			}
		}
	}

	function dekrypterMedCesar() {
		resultat = '';

		for (const tegn of tekst) {
			if (tegn === ' ') {
				resultat += ' ';
				continue;
			}

			if (storeBokstaver.includes(tegn)) {
				const posisjon = storeBokstaver.indexOf(tegn);
				const nyPosisjon = (posisjon - Number(nokkel) + 26) % 26;
				resultat += storeBokstaver[nyPosisjon];
			}

			if (smaBokstaver.includes(tegn)) {
				const posisjon = smaBokstaver.indexOf(tegn);
				const nyPosisjon = (posisjon - Number(nokkel) + 26) % 26;
				resultat += smaBokstaver[nyPosisjon];
			}
		}
	}

	function krypterMedVigenere() {
		resultat = '';
		const nykkel = vigenereNokkel.toUpperCase();
		let nøkkelIndeks = 0;

		for (const tegn of tekst) {
			if (tegn === ' ') {
				resultat += ' ';
				continue;
			}

			const erStor = tegn >= 'A' && tegn <= 'Z';
			const alfabet = erStor ? storeBokstaver : smaBokstaver;
			const posisjon = alfabet.indexOf(tegn);
			const nøkkelTegn = nykkel[nøkkelIndeks % nykkel.length];
			const nøkkelShift = nøkkelTegn.charCodeAt(0) - 65;
			const nyPosisjon = (posisjon + nøkkelShift) % 26;
			resultat += alfabet[nyPosisjon];
			nøkkelIndeks++;
		}
	}

	function dekrypterMedVigenere() {
		resultat = '';
		const nykkel = vigenereNokkel.toUpperCase();
		let nøkkelIndeks = 0;

		for (const tegn of tekst) {
			if (tegn === ' ') {
				resultat += ' ';
				continue;
			}

			const erStor = tegn >= 'A' && tegn <= 'Z';
			const alfabet = erStor ? storeBokstaver : smaBokstaver;
			const posisjon = alfabet.indexOf(tegn);
			const nøkkelTegn = nykkel[nøkkelIndeks % nykkel.length];
			const nøkkelShift = nøkkelTegn.charCodeAt(0) - 65;
			const nyPosisjon = (posisjon - nøkkelShift + 26) % 26;
			resultat += alfabet[nyPosisjon];
			nøkkelIndeks++;
		}
	}

	function krypter() {
		if (!proverInput()) {
			return;
		}

		if (metode === 'Vigenère') {
			krypterMedVigenere();
			return;
		}

		krypterMedCesar();
	}

	function dekrypter() {
		if (!proverInput()) {
			return;
		}

		if (metode === 'Vigenère') {
			dekrypterMedVigenere();
			return;
		}

		dekrypterMedCesar();
	}
</script>

<h1>Caesar- og Vigenère-kryptering</h1>

<select name="metode" id="metode" bind:value={metode}>
	<option value="Cesar">Cesar</option>
	<option value="Vigenère">Vigenère</option>
</select>

<label for="tekst">Tekst:</label>
<input id="tekst" bind:value={tekst} />

{#if metode === 'Vigenère'}
	<label for="vigenereNokkel">Nøkkel:</label>
	<input id="vigenereNokkel" bind:value={vigenereNokkel} />
{:else}
	<label for="nokkel">Nøkkel:</label>
	<input id="nokkel" type="number" min="0" max="25" bind:value={nokkel} />
{/if}

<button onclick={krypter}>Krypter</button>
<button onclick={dekrypter}>Dekrypter</button>

<p>Resultat: {resultat}</p>

{#if feilmelding}
	<p>{feilmelding}</p>
{/if}