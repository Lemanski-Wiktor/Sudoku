<script>
	import { Router, Link, Route } from "svelte-routing";
	import LoadFile from "./components/LoadFile.svelte";
	import Game from "./components/Game.svelte";

	export let url = "";

</script>
	
<main>
	<Router url="{url}">

		<Route path='' component='{LoadFile}'/>
		<Route path='game' component='{Game}'/>

	</Router>
</main>

<style>
	main {
		width: 100vw;
		height: 100vh;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}
</style>