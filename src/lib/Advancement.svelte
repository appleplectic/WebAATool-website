<script>
    import { BASE_PATH } from "./config.js";

    export let status = "incomplete";
    export let asset_name = "adventure.png";
    export let advancement = "Adventure";

    advancement = advancement.replaceAll("\\\"", "\"")

    let base = "frame_modern_";
    let size = 78;
    let stateSuffix = {
        incomplete: "",
        partial: "_partial",
        complete: "_complete"
    };

    $: backImg = `${base}back${stateSuffix[status]}^${size}.png`;
    $: borderImg = `${base}border${stateSuffix[status]}^${size}.png`;
</script>

<style>
    .frame {
        position: relative;
        width: 52px;
        height: 52px;
    }

    .img-background,
    .border {
        position: absolute;
        top: 0;
        left: 0;
        width: 52px;
        height: 52px;
        image-rendering: pixelated;
    }

    .icon {
        position: absolute;
        width: 16px;
        height: 16px;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%) scale(2);
        image-rendering: pixelated;
        border-radius: 2px;
    }

    .background-mask {
        position: absolute;
        top: 0;
        left: 0;
        width: 52px;
        height: 52px;
        background-color: #231F17;
        -webkit-mask-image: var(--back-img);
        -webkit-mask-size: cover;
        -webkit-mask-repeat: no-repeat;
        mask-image: var(--back-img);
        mask-size: cover;
        mask-repeat: no-repeat;
        image-rendering: pixelated;
    }

    .border-mask {
        position: absolute;
        top: 0;
        left: 0;
        width: 52px;
        height: 52px;
        background-color: #4c4e4c;
        -webkit-mask-size: cover;
        -webkit-mask-repeat: no-repeat;
        mask-size: cover;
        mask-repeat: no-repeat;
        image-rendering: pixelated;
    }

    .frame-wrapper {
        display: flex;
        flex-direction: column;
        align-items: center;
        width: fit-content;
        margin-bottom: 4px;
    }

    .label {
        margin-top: 2px;
        font-size: 10px;
        text-align: center;
    }
</style>

<div class="frame-wrapper">
    <div class="frame" style="--back-img: url('{BASE_PATH}advancement_frame_modern/{backImg}')">
        {#if status === "incomplete"}
            <div
                    class="background-mask"
                    style="
                    -webkit-mask-image: var(--back-img);
                    mask-image: var(--back-img);
                "
            ></div>
        {:else}
            <img class="img-background" src="{BASE_PATH}advancement_frame_modern/{backImg}" alt="background" />
        {/if}

        {#if status === "incomplete"}
            <div
                    class="border-mask"
                    style="
                --border-img: url('{BASE_PATH}advancement_frame_modern/{borderImg}');
                -webkit-mask-image: var(--border-img);
                mask-image: var(--border-img);
            "
            ></div>
        {:else}
            <img class="border" src="{BASE_PATH}advancement_frame_modern/{borderImg}" alt="border" />
        {/if}

        <img class="icon" src="{BASE_PATH}advancements/{asset_name}" alt="advancement"/>
    </div>
    <div class="label">{advancement}</div>
</div>