<script>
  import ProgressBar from "./ProgressBar.svelte";

  export let numCompleted = 0;
  export let numTotal = 80;
  export let IGT = "0:00:00";

  let width = 50;
  let scale = 1.5;

  $: percentage = Math.floor((numCompleted / numTotal) * 100);
</script>

<div style="width: {width * scale}vw; margin: 0 auto; margin-bottom: 8px">
  <div style="text-align: center; font-size: 0.75em; margin-bottom: 0px;">
    {numCompleted} / {numTotal} Advancements ({percentage}%) - Last Updated {IGT} IGT
  </div>

  <ProgressBar progress={percentage} {scale} {width}/>
</div>

