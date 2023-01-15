<script lang="ts">
    import Buttons from "../components/Buttons.svelte";
import Joystick from "../components/joystick.svelte";

    let armed = false;

    function toggleArmed(){
        armed = !armed;
    }
</script>

<div class="layout">
    <Buttons />


    <div class="joystick-cont">
        <Joystick ch1={1} ch2={2} />

        <Joystick ch1={3} ch2={4} bounceBack={false} />
    </div>
</div>

<style lang="scss" >

    .layout{
        display: flex;
        flex-direction: row;
        align-items: center;
        justify-content: space-around;
        height: 100vh;
    }


    .joystick-cont {
    	display: flex;
    	flex-direction: row;
    	align-items: center;
    	justify-content: space-around;
    	height: 300px;
    	width: 100vw;
        overflow: hidden;
    }
</style>