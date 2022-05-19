<script>

	// Imports:
	import { onMount } from 'svelte';

	// Initializations & Exports:
	export let connection = {
		status: 'disconnected',
		info: null
	}
	const chains = {
		'0x1': 'Ethereum',
		'0x38': 'BSC',
		'0x89': 'Polygon',
		'0xfa': 'Fantom',
		'0xa86a': 'Avalanche',
		'0x63564c40': 'Harmony',
		'0x19': 'Cronos',
		'0xa': 'Optimism',
		'0xa4b1': 'Arbitrum'
	}

	// Function to connect to wallet:
	const connect = async () => {
		if(typeof window.ethereum !== 'undefined') {
			try {
				let chainID = await fetchChainID();
				let accounts = await fetchAccounts();
				if(chainID && accounts.length > 0) {
					connection.info = {
						account: accounts[0],
						chain: chainID
					}
					connection.status = 'connected';
				}
			} catch(err) {
				connection.status = 'disconnected';
				if(err.code === 4001) {
					console.error('Wallet connection rejected.');
				} else {
					console.error('Something went wrong while connecting to wallet.');
				}
			}
		}
	}

	// Function to fetch chain ID from wallet:
	const fetchChainID = async () => {
		try {
			let chainID = await window.ethereum.request({ method: 'eth_chainId' });
			return chainID;
		} catch {
			console.error('Could not fetch wallet\'s chain ID.');
			return undefined;
		}
	}

	// Function to fetch accounts from wallet:
	const fetchAccounts = async () => {
		try {
			let accounts = await window.ethereum.request({ method: 'eth_requestAccounts' });
			return accounts;
		} catch {
			console.error('Could not fetch wallet\'s account list.');
			return [];
		}
	}

	// Function to handle network changes:
	const handleNetworkChange = async () => {
		connection.status = 'disconnected';
		if(connection.info) {
			try {
				let chainID = await fetchChainID();
				if(chainID) {
					connection.info.chain = chainID;
					connection.status = 'connected';
				}
			} catch {
				console.error('Something went wrong while changing wallet networks.');
			}
		}
	}

	// Function to handle network changes:
	const handleAccountChange = async (accounts) => {
		connection.status = 'disconnected';
		if(connection.info && accounts.length > 0) {
			connection.info.account = accounts[0];
			connection.status = 'connected';
		}
	}
  
	onMount(async () => {
    
		// Handling wallet events:
		window.ethereum.on('chainChanged', handleNetworkChange);
		window.ethereum.on('accountsChanged', handleAccountChange);

	});

</script>

<!-- #################################################################################################### -->

<div id="wallet">
	{#if connection.status === 'connected' && connection.info}

		<!-- Displaying User's Network & Connected Address -->
		<span id="network">{chains[connection.info.chain] ? chains[connection.info.chain] : 'Unidentified Network'}</span>
		<span id="address">{connection.info.account.slice(0, 6)}...{connection.info.account.slice(-4)}</span>

	{:else}

		<!-- Displaying Connection Button -->
		<button id="connectButton" on:click={() => connect()}>Connect Wallet</button>

	{/if}
</div>

<!-- #################################################################################################### -->

<style>

	/* CSS Goes Here */

</style>