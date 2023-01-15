<script lang="ts">
    import { onMount } from 'svelte';

    type pos = {
        x: number,
        y: number
    }

    let joystick;
    let thumbStick;
    let joystickSize: number;
    let bounceBack = true;
    let ch1 = 1;
    let ch2 = 3;

    let joystickPos: pos = {
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

        const ret = {};
        ret[`ch${ch1}`] = xMapped;
        ret[`ch${ch2}`] = yMapped;

        return ret;
    }

    function updateJoyStick(pos: Pos) {
        thumbStick.style.left = `${pos.x}px`;
        thumbStick.style.top = `${pos.y}px`;
    }

    function touchStart(e: TouchEvent) {
        const touch = e.touches[0]
        const pos = findPos({
            x: touch.clientX,
            y: touch.clientY
        })

        updateJoyStick(pos);
        sendApiRequest(pos);
    }

    function touchMove(e: TouchEvent) {
        const touch = e.touches[0]
        const pos = findPos({
            x: touch.clientX,
            y: touch.clientY
        })


        updateJoyStick(pos);
        sendApiRequest(pos);
    }

    function touchEnd(e: TouchEvent) {
        // Recenter
        if (bounceBack) {
            updateJoyStick({
                x: (joystickSize / 2 )- 2,
                y: (joystickSize / 2) - 2
            })
        }
    }

    function sendApiRequest(pos: Pos) {
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

        joystickSize = joystick.offsetWidth;
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

<style lang="scss" />