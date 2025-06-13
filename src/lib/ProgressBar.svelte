<script>
  import {BASE_PATH} from "./config.js";

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
    background: no-repeat;
    width: 20px;
  }

  .active-middle {
    background: repeat-x;
    height: 100%;
  }

  .active-edge {
    background: no-repeat;
    width: 5px;
  }

  .active-right {
    background: no-repeat;
    width: 20px;
  }

  .inactive-left {
    background: no-repeat;
    width: 20px;
  }

  .inactive-middle {
    background: repeat-x;
    height: 100%;
    flex-grow: 1;
  }

  .inactive-right {
    background: no-repeat;
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
    <div
      class="segment inactive-left"
      style={`background: url('${BASE_PATH}progress_bar_modern/bar_modern_inactive_left.png') no-repeat;`}
    ></div>
    <div
      class="segment inactive-middle"
      style={`background: url('${BASE_PATH}progress_bar_modern/bar_modern_inactive_middle.png') repeat-x;`}
    ></div>
    <div
      class="segment inactive-right"
      style={`background: url('${BASE_PATH}progress_bar_modern/bar_modern_inactive_right.png') no-repeat;`}
    ></div>

    <div class="active-bar" style="width: {progress}%;">
      <div
        class="segment active-left"
        style={`background: url('${BASE_PATH}progress_bar_modern/bar_modern_active_left.png') no-repeat;`}
      ></div>
      <div
        class="segment active-middle"
        style={`background: url('${BASE_PATH}progress_bar_modern/bar_modern_active_middle.png') repeat-x; flex-grow: 1;`}
      ></div>
      {#if progress === 100}
        <div
          class="segment active-right"
          style={`background: url('${BASE_PATH}progress_bar_modern/bar_modern_active_right.png') no-repeat;`}
        ></div>
      {:else}
        <div
          class="segment active-edge"
          style={`background: url('${BASE_PATH}progress_bar_modern/bar_modern_active_edge.png') no-repeat;`}
        ></div>
      {/if}
    </div>
  </div>
</div>
