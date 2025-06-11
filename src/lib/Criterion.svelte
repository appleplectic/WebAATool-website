<script>
    import { BASE_PATH } from './config.js';

    export let name = "black";
    export let text = "Black";
    export let isComplete = false;

    let ending = ".png";

    if (name === "enchanted_golden_apple") {
        ending = ".gif";
    }
</script>

<style>
    .image-text-container {
        position: relative;
        display: inline-flex;
        align-items: center;
    }

    .icon {
        width: 16px;
        height: 16px;
        margin-right: 5px;
        image-rendering: pixelated;
    }

    .content {
        position: relative;
        z-index: 0;
        align-items: center;
        display: flex;
    }

    .inactive {
        filter: grayscale(100%) brightness(70%);
        opacity: 0.6;
    }

    .icolabel {
        font-size: 8px;
        line-height: 1;
    }
</style>

<div class="image-text-container" class:inactive={!isComplete}>
    <div class="content">
        <img class="icon" src={BASE_PATH + "criteria/" + name + ending} alt="icon" />
        <span class="icolabel">{text}</span>
    </div>
</div>