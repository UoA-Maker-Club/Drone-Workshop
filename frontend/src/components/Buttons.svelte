<script lang="ts">
    let armed = false;

    async function toggleArmed(){
        armed = !armed;

        // TODO URL
        fetch("https://localhost:3000/api/arm", {
            method: "POST",
            headers: {
                "Content-Type": "application/json"
            },
            body: JSON.stringify({
                armed: armed
            })
        })

        // TODO react based on fail or success
    }
</script>

<div class="buttons">
    <button class={armed?"active":""} on:click={toggleArmed}> {armed? "Armed" : "Arm"} </button>
</div>


<style lang="scss">
    .buttons {
	    display: flex;
	    flex-direction: row;
	    align-items: center;
        position: absolute;
        left: 5px;
        top: 5px;

	    button {
	    	width: 100px;
	    	height: 50px;
	    	border: none;
	    	border-radius: 10px;
	    	border: 2px solid #66f;
            font-weight: bold;
            font-size: 16px;

            &.active{
                background-color: #66f;
                color: white;
            }
	    }
    }
</style>