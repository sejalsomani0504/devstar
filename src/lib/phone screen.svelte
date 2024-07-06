<script>
    import { createEventDispatcher } from 'svelte';

    // Define your background image URL
    export let background = 'https://static.vecteezy.com/system/resources/previews/011/731/356/non_2x/flat-illustration-of-sea-waves-clouds-and-sun-views-suitable-for-poster-banner-advertising-promotion-and-background-free-vector.jpg';

    // Initial data for phone screens
    let phoneScreens = [
        { id: 1, content: "Hello", width: 150, height: 100 },
        { id: 2, content: "World", width: 150, height: 100 },
        { id: 3, content: "Svelte", width: 150, height: 100 },
        { id: 4, content: "Resize", width: 150, height: 100 },
        { id: 5, content: "Text", width: 150, height: 100 }
    ];

    // State to manage the selected screen
    let selectedScreenId = null;

    // Event dispatcher for emitting custom events
    const dispatch = createEventDispatcher();

    // Function to select a screen
    function selectScreen(id) {
        selectedScreenId = id;
        dispatch('select', { id });
    }

    // Function to handle resizing of screens
    function handleResize(event, screen, direction) {
        event.preventDefault();
        event.stopPropagation();

        const startX = event.clientX;
        const startY = event.clientY;
        const startWidth = screen.width;
        const startHeight = screen.height;

        function onMouseMove(e) {
            const diffX = e.clientX - startX;
            const diffY = e.clientY - startY;

            switch (direction) {
                case 'bottom':
                    screen.height = Math.max(startHeight + diffY, 50); // Adjust minimum height as needed
                    break;
                case 'top':
                    screen.height = Math.max(startHeight - diffY, 50); // Adjust minimum height as needed
                    break;
                case 'right':
                    screen.width = Math.max(startWidth + diffX, 30); // Adjust minimum width as needed
                    break;
                case 'left':
                    screen.width = Math.max(startWidth - diffX, 30); // Adjust minimum width as needed
                    break;
                default:
                    break;
            }

            phoneScreens = [...phoneScreens];
        }

        function onMouseUp() {
            window.removeEventListener('mousemove', onMouseMove);
            window.removeEventListener('mouseup', onMouseUp);
        }

        window.addEventListener('mousemove', onMouseMove);
        window.addEventListener('mouseup', onMouseUp);
    }
</script>

<style>
    /* Styles for resize handles */
    .resize-handle {
        position: absolute;
        width: 20px;
        height: 20px;
        background: rgba(0, 0, 0, 0.5);
        border-radius: 50%;
        z-index: 10;
        cursor: pointer;
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .resize-handle::after {
        content: '';
        display: block;
        width: 10px;
        height: 10px;
        border-left: 2px solid #fff;
        border-bottom: 2px solid #fff;
        transform: rotate(45deg);
    }

    /* Specific cursor and position for each resize handle */
    .bottom {
        bottom: -10px;
        left: calc(50% - 10px);
        cursor: s-resize;
    }

    .top {
        top: -10px;
        left: calc(50% - 10px);
        cursor: n-resize;
    }

    .right {
        top: calc(50% - 10px);
        right: -10px;
        cursor: e-resize;
    }

    .left {
        top: calc(50% - 10px);
        left: -10px;
        cursor: w-resize;
    }

    /* Container styles for each screen */
    .screen-container {
        position: relative;
        width: 50px;
        padding-top: calc(100% / 2);
        background-size: cover;
        background-position: center;
        border: 4px solid black;
        border-bottom: 20px solid black;
        border-top: 10px solid black;
        overflow: hidden;
        resize: both;
        min-width: 30px; /* Adjust as needed */
        min-height: 50px; /* Adjust as needed */
        max-width: 200px; /* Adjust as needed */
        gap: 40px;
        max-height: 270px; /* Adjust as needed */
    }
</style>

<!-- HTML structure for displaying screens -->
<div class="flex-1 p-4 mb-44 mt-11 v" style="background: url('{background}');">
    <div class="flex justify-between items-start space-x-3">
        {#each phoneScreens as screen}
            <div class="flex flex-col items-center w-1/5">
                <div
                        class="relative cursor-pointer rounded-3xl overflow-hidden screen-container {selectedScreenId === screen.id ? 'ring-2 ring-blue-500' : ''}"
                        style="width: {screen.width}px; height: {screen.height}px; z-index: {6 - screen.id};"
                        on:click={() => selectScreen(screen.id)}>
                    <!-- Resize handles for each direction -->
                    <div class="resize-handle right" on:mousedown={(event) => handleResize(event, screen, 'right')}></div>
                    <div class="resize-handle left" on:mousedown={(event) => handleResize(event, screen, 'left')}></div>
                    <div class="resize-handle top" on:mousedown={(event) => handleResize(event, screen, 'top')}></div>
                    <div class="resize-handle bottom" on:mousedown={(event) => handleResize(event, screen, 'bottom')}></div>
                    <!-- Content to be resized (text in this case) -->
                    <div class="absolute inset-0 flex items-center justify-center">
                        <p class="text-white text-lg">{screen.content}</p>
                    </div>
                </div>
            </div>
        {/each}
    </div>
</div>