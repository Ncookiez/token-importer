<script>

	// Imports:
	import weaver from 'weaverfi';
	import Wallet from '../lib/Wallet.svelte';

	// Initializations:
	const allTokens = weaver.getAllTokens();
	let connection;

	// Reactive Variables:
	$: tokens = fetchChainTokens(connection);

	// Function to fetch a chain's token list:
	const fetchChainTokens = (connection) => {
		let tokenList = [];
		if(connection && connection.status === 'connected' && connection.info && connection.info.chain) {
			let chain = fetchChain(connection.info.chain);
			if(chain) {
				tokenList.push(...allTokens[chain]);
			}
		}
		return tokenList;
	}

	// Function to fetch a chain's name:
	const fetchChain = (chainID) => {
		let chain;
		switch(chainID) {
			case '0x1':
				chain = 'eth';
				break;
			case '0x38':
				chain = 'bsc';
				break;
			case '0x89':
				chain = 'poly';
				break;
			case '0xfa':
				chain = 'ftm';
				break;
			case '0xa86a':
				chain = 'avax';
				break;
			case '0x63564c40':
				chain = 'one';
				break;
			case '0x19':
				chain = 'cronos';
				break;
			case '0xa':
				chain =' op';
				break;
			case '0xa4b1':
				chain = 'arb';
				break;
		}
		return chain;
	}

	// Function to import token to web wallet:
	const importToken = (token) => {
		window.ethereum.request({
			method: 'wallet_watchAsset',
			params: {
				type: 'ERC20',
				options: {
					address: token.address,
					symbol: token.symbol,
					decimals: token.decimals,
					image: token.logo
				}
			}
		});
	}
	
</script>

<!-- #################################################################################################### -->

<!-- SvelteKit Dynamic Header -->
<svelte:head>
	<title>Token Importer</title>
	<meta name="description" content="A quick proof-of-concept app to import tokens with their logos onto web wallets." />
</svelte:head>

<!-- Wallet Connection -->
<section>
	<Wallet bind:connection />
</section>

<!-- Token Importer -->
<section>

	<!-- Search Bar -->

	<!-- Token List -->
	<div class="tokenList">
		{#each tokens as token}
			<span class="token">
				<img src="{token.logo}" alt="{token.symbol} Logo">
				{token.symbol}
				<button on:click={() => importToken(token)}>+</button>
			</span>
		{/each}
	</div>
</section>

<!-- #################################################################################################### -->

<style>

	section {
		display: flex;
		justify-content: center;
		align-items: center;
		padding: 1em 10vw;
	}

	section:last-of-type {
		padding-bottom: 100px;
	}

	.tokenList {
		display: flex;
		flex-direction: column;
	}

	.token > img {
		height: 2em;
	}
	
</style>