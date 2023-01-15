<script lang="ts">
    import Joystick from "../components/joystick.svelte";
</script>

<div class="buttons">
    <button> btn 1 </button>
    <button> btn 2 </button>
    <button> btn 3 </button>
</div>

<div class="joystick-cont">
    <Joystick ch1={1} ch2={2} />

    <Joystick ch1={3} ch2={4} bounceBack={false} />
</div>

<style lang="scss" >
    .buttons {
	    display: flex;
	    flex-direction: row;
	    align-items: center;
	    justify-content: space-around;
	    height: 30px;
	    margin-top: 10px;

	    button {
	    	width: 100px;
	    	height: 30px;
	    	border: none;
	    	border-radius: 20px;
	    	border: 2px solid #66f;
	    }
    }

    .joystick-cont {
    	display: flex;
    	flex-direction: row;
    	align-items: center;
    	justify-content: space-around;
    	height: calc(100vh - 40px);
    	width: 100vw;
        overflow: hidden;
    }
</style>