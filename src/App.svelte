<script>
	import { Router, Link, Route } from "svelte-routing";
	import LoadFile from "./components/LoadFile.svelte";
	import Game from "./components/Game.svelte";

	 let url = "";

</script>
	
<main>
	<Router url="{url}">
		<Route path='/'>
			<LoadFile on:send={(event)=>console.log(event.detail)}/>
		</Route>
		
		<Route path='game'>
			<Game/>
		</Route>
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