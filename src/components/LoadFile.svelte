<script>
    import {Link} from "svelte-routing";
    import { createEventDispatcher } from "svelte";

    const dispatch = createEventDispatcher()
    let json = null
    let isSend = true

    async function loadJSON(){
        const file = document.querySelector('#data').files[0]
        const text = await file.text()
        json = JSON.parse(text)
    }

    function sendData(){
        if(json == null){
            isSend = false
            dispatch('send',isSend)
            console.log("Nie wybrano pliku!");
        }else{
            isSend = true
            dispatch('send',isSend)
            setCookie(json['sudoku'],1)
        }
    }
    function setCookie(value,exdays) {
        const d = new Date();
        d.setTime(d.getTime() + (exdays*24*60*60*1000));
        let expires = "expires="+ d.toUTCString();

        value = JSON.stringify(value)
        document.cookie = 'sudoku=' + value + ";" + expires + ";path=/";
    }

</script>

<div id="container">
    <form action="">
        <h2>Load data for Sudoku:</h2>
        <input type="file" accept="aplication/json" id="data" on:change={loadJSON}>
        <Link to='/game'><button on:click={sendData}>Load</button></Link>
    </form>
</div>

<style>
    form{
        width: 55vw;
        height: 40vh;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: space-evenly;
        background-color: azure;
        border: 1px solid black;
        box-shadow: 0 1px 5px 1px black;
    }
    #data{
        width: 250px;
    }
</style>