<script lang="ts">
    import { onMount } from 'svelte';
    export let ch1: number;
    export let ch2: number;
    export let bounceBack = true;

    type Pos = {
        x: number,
        y: number
    }

    let joystick: HTMLElement;
    let thumbStick: HTMLElement;
    let joystickSize: number;


    let joystickPos: Pos = {
        x: 0,
        y: 0
    }

    function findPos(pos: Pos): Pos {
        return {
            x: pos.x - joystickPos.x,
            y: pos.y - joystickPos.y
        }
    }

    function mapChannel(pos: Pos) {
        // map from -255 to 255
        let xMapped = ((pos.x / joystickSize) * 510) -255;
        if(xMapped > 255) xMapped = 255;
        else if (xMapped < -255) xMapped = -255;

        let yMapped = ((pos.y / joystickSize) * 510) - 255;
        yMapped = -yMapped; // Invert y
        if(yMapped > 255) yMapped = 255;
        else if (yMapped < -255) yMapped = -255;

        const ret: any = {};
        ret[`ch${ch1}`] = xMapped;
        ret[`ch${ch2}`] = yMapped;

        return ret;
    }

    function updateJoyStick(pos?: Pos) {
        thumbStick.style.translate = `${(pos??joystickPos).x}px ${(pos??joystickPos).y}px`;
    }

    function touchStart(e: TouchEvent) {
        e.preventDefault();

        const touch = e.touches[0]
        const pos = findPos({
            x: touch.clientX,
            y: touch.clientY
        })

        sendApiRequest(pos);
    }

    function touchMove(e: TouchEvent) {
        e.preventDefault();

        const touch = e.touches[0]
        const pos = findPos({
            x: touch.clientX,
            y: touch.clientY
        })


        updateJoyStick(pos);
        sendApiRequest(pos);
    }

    function resetJoyStick(){
        updateJoyStick({
            x: (joystickSize / 2 )- 2,
            y: (joystickSize / 2) - 2
        })
    }

    function touchEnd() {
        if (bounceBack) resetJoyStick();
    }

    async function sendApiRequest(pos: Pos) {
        const body = mapChannel(pos);

        // TODO update URL
        fetch(
            'http://localhost:3000/api/joystick',
            {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(body)
            }
        )
    }

    onMount(() => {
        joystickPos = {
            x: joystick.offsetLeft,
            y: joystick.offsetTop
        }

        joystick.addEventListener('touchstart', (e) => touchStart(e));
        joystick.addEventListener('touchmove', (e) => touchMove(e));
        joystick.addEventListener('touchend', (e) => touchEnd(e));
        joystick.addEventListener('touchcancel', (e) => touchEnd(e));


        joystickSize = joystick.offsetWidth;
        resetJoyStick();
    })
</script>

<div bind:this={joystick} class="joystick">
    <div class="markers" >
        <div class="line"></div>
        <div class="line"></div>
        <div class="line"></div>
        <div class="line"></div>
        <div class="center"></div>
    </div>

    <div class="thumb-stick" bind:this={thumbStick}/>
</div>

<style lang="scss" >
    $main: #66f;
    $thumbWidth: 40px;

    .joystick {
    	width: 200px;
    	height: 200px;
    	border-radius: 50%;
    	background-color: white;
    	position: relative;
    	border: 2px solid $main;
    	box-sizing: border-box;
    }

    .markers {
    	.line {
    		position: absolute;
    		height: 2px;
    		width: 20px;
    		background-color: $main;
    		transform-origin: 0;

    		&:nth-child(1) {
    			top: 0;
    			left: 50%;
    			rotate: 90deg;
    			translate: 0 -2px;
    		}

    		&:nth-child(2) {
    			top: 50%;
    			left: 0;
    			translate: 0 -2px;
    		}

    		&:nth-child(3) {
    			bottom: 0;
    			left: 50%;
    			translate: 0 -18px;
    			rotate: 90deg;
    		}

    		&:nth-child(4) {
    			top: 50%;
    			right: 0;
    			translate: 0 -2px;
    		}
    	}

    	.center {
    		position: absolute;
    		top: 50%;
    		left: 50%;
    		transform: translate(-50%, -50%);
    		width: $thumbWidth;
    		height: $thumbWidth;
    		background-color: #777;
    		border-radius: 50%;
    	}
    }

    .thumb-stick {
    	width: $thumbWidth;
    	height: $thumbWidth;
    	border-radius: 50%;
    	background-color: $main;
    	position: absolute;
    	top: 0;
    	left: 0;
    	transform: translate(-50%, -50%);
        background-image: linear-gradient(146deg, #567dff 0%, #9f42d1 20%, #f04ab9 35%, #ff25c7 50%, #ff3c6d 75%, #ff856a 100%);
    }
</style>