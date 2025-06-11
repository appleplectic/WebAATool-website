<script>
    export let progress = 0;
    export let width;
    export let scale = 1.5;
</script>

<style>
    .progress-container {
        display: flex;
        height: 10px;
        overflow: hidden;
    }

    .progress-container,
    .segment {
        image-rendering: pixelated;
    }

    .segment {
        display: flex;
    }

    .active-left {
        background: url('/progress_bar_modern/bar_modern_active_left.png') no-repeat;
        width: 20px;
    }

    .active-middle {
        background: url('/progress_bar_modern/bar_modern_active_middle.png') repeat-x;
        height: 100%;
    }

    .active-edge {
        background: url('/progress_bar_modern/bar_modern_active_edge.png') no-repeat;
        width: 5px;
    }

    .active-right {
        background: url('/progress_bar_modern/bar_modern_active_right.png') no-repeat;
        width: 20px;
    }

    .inactive-left {
        background: url('/progress_bar_modern/bar_modern_inactive_left.png') no-repeat;
        width: 20px;
    }

    .inactive-middle {
        background: url('/progress_bar_modern/bar_modern_inactive_middle.png') repeat-x;
        height: 100%;
        flex-grow: 1;
    }

    .inactive-right {
        background: url('/progress_bar_modern/bar_modern_inactive_right.png') no-repeat;
        width: 20px;
    }

    .active-bar {
        display: flex;
        position: absolute;
        height: 100%;
        overflow: hidden;
    }
</style>

<div
    style="
        display: inline-block;
        transform-origin: top left;
        image-rendering: pixelated;
        scale: {scale};
    "
>
    <div class="progress-container" style="width: {width}vw; position: relative;">
        <div class="segment inactive-left"></div>
        <div class="segment inactive-middle"></div>
        <div class="segment inactive-right"></div>

        <div class="active-bar" style="width: {progress}%;">
            <div class="segment active-left"></div>
            <div class="segment active-middle" style="flex-grow: 1;"></div>
            {#if progress === 100}
                <div class="segment active-right"></div>
            {:else}
                <div class="segment active-edge"></div>
            {/if}
        </div>
    </div>
</div>