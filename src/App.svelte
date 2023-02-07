<script>
	import { Router, Link, Route } from "svelte-routing";
	import LoadFile from "./components/LoadFile.svelte";
	import Game from "./components/Game.svelte";
	import NoFile from "./components/NoFile.svelte";

	let url = "";
	let isSend = true
	let json = null

	function sendData(event){
		isSend = event.detail;
	}
	function getSudoku(event){
		json = event.detail
	}

</script>
	
<main>
	<Router url="{url}">
		<Route path='/'>
			<LoadFile on:send={sendData} on:data={getSudoku}/>
		</Route>

		<Route path='game'>
			{#if isSend}
				<Game/>
			{:else}
				<NoFile/>
			{/if}
		</Route>
	</Router>
</main>

<style>
	main {
		width: 100vw;
		height: 100%;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		background-color: #084c61;
	}
	
</style>