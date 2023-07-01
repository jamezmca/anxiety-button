<script>
    import { onDestroy, onMount } from "svelte";
    const start = new Date().getTime();

    function getElapsed() {
        return new Date().getTime() - start;
    }

    let m = { x: 0, y: 0 };
    let btnOffsetHeight;
    let btnOffsetWidth;
    let shakeBtn;
    let direction = null;
    let velocity = null;
    let won = false;

    function handleMousemove(event) {
        //Relative to viewport
        m.x = event.clientX;
        m.y = event.clientY;
        //getBoundingClientRect is also relative to view port
        const btnBoundaries = shakeBtn.getBoundingClientRect();

        let btnCentroid = {
            y: btnBoundaries.y + btnOffsetHeight / 2,
            x: btnBoundaries.x + btnOffsetWidth / 2,
        }; //relative to view port

        let maxMagnitude = Math.pow(
            Math.pow(btnCentroid.y, 2) + Math.pow(btnCentroid.x, 2),
            0.5
        );

        let magnitudeVectors = {
            dx: btnCentroid.x - m.x,
            dy: btnCentroid.y - m.y,
        }; //from mouse to btn

        let magnitude = Math.pow(
            Math.pow(magnitudeVectors.dx, 2) + Math.pow(magnitudeVectors.dy, 2),
            0.5
        );
        // return
        let magnitudeDecimal = magnitude / maxMagnitude; //equates to shake speed
        let elapsed = getElapsed();
        let prevAnimationCompletion = elapsed % 5000;
        const shakeBtnElement = document.getElementById("shakeBtn");
        shakeBtnElement.animate(
            [
                // keyframes
                { transform: `translateX(${0}%)` },
                {
                    transform: `translateX(${
                        1 - magnitudeDecimal > 0.6
                            ? 10 * (1 - magnitudeDecimal)
                            : 0
                    }%)`,
                },
                { transform: `translateX(${0}%)` },
                {
                    transform: `translateX(${
                        1 - magnitudeDecimal > 0.6
                            ? -10 * (1 - magnitudeDecimal)
                            : 0
                    }%)`,
                },
                { transform: `translateX(${0}%)` },
            ],
            {
                // timing options
                duration: 2000 - 1950 * (1 - magnitudeDecimal),
                iterations: Infinity,
                delay: -prevAnimationCompletion,
            }
        );
    }
</script>

<main
    class="min-h-screen relative flex flex-col p-4 items-center justify-center text-slate-800"
    on:mousemove={handleMousemove}
>
    <p class="text-xs sm:text-sm select-none z-0 text-slate-400 sm:hidden">
        It's kinda doesn't work on a mobile device but oh well
    </p>
    <div
        class={"absolute inset-0 flex items-center bg-white justify-center flex-col gap-10 z-10 " +
            (won
                ? " opacity-100 pointer-events-auto"
                : " opacity-0 pointer-events-none")}
    >
        <p class="text-rose-400">Well I didn't want you to subscribe anyway</p>
        <i class="fa-solid fa-face-sad-cry text-6xl text-rose-400" />
        <div class="absolute bottom-1 right-1">
            <a
                href="https://smoljames.com"
                target="_blank"
                class="text-blue-400 hover:text-blue-600">Smoljames</a
            >
        </div>
    </div>

    <button
        id="shakeBtnContainer"
        on:mouseenter={() => {
            const shakeBtnContainer =
                document.getElementById("shakeBtnContainer");
            shakeBtnContainer.style.position = "absolute";
            shakeBtnContainer.style.top = `${Math.random() * 90}%`;
            shakeBtnContainer.style.left = `${Math.random() * 90}%`;
        }}
        bind:this={shakeBtn}
        bind:offsetWidth={btnOffsetWidth}
        bind:offsetHeight={btnOffsetHeight}
        class=" p-4 cursor-auto hidden sm:flex"
    >
        <button
            id="shakeBtn"
            on:click={(e) => {
                e.stopPropagation();
                won = true;
            }}
            class="bg-rose-400 anxiety text-white rounded py-2 px-4 rounded-md mx-auto duration-200 text-sm sm:text-base"
            >Unsubscribe</button
        >
    </button>
</main>
