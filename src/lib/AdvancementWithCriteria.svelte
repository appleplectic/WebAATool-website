<script>
  import Criterion from "./Criterion.svelte";
  import Advancement from "./Advancement.svelte";
  import ProgressBar from "./ProgressBar.svelte";

  export let pair;
  let count;
  let total;
  $: criteriaEntries = Object.entries(pair.value["criteria"])
  $: {
    count = 0;
    for (const [_, value] of criteriaEntries) {
      if (value["isComplete"] === true) {
        count++;
      }
    }
    total = criteriaEntries.length;
  }
</script>

<style>
  .criteria-grid {
    display: grid;
  }

  .criteria-grid.one-column {
    grid-template-columns: 1fr;
  }

  .criteria-grid.two-column {
    grid-template-columns: repeat(2, auto);
    justify-content: center;
    column-gap: 5px;
  }

  .advancement-criteria-wrapper {
    display: block;
    align-items: center;
    flex-direction: column;
    margin-top: 10px;
  }

  .advancement-wrapper {
    width: 100%;
    display: flex;
    justify-content: center;
  }

  .bar {
    margin-bottom: 8px;
    display: grid;
  }

  .label-counter {
    font-size: 10px;
    text-align: center;
  }
</style>

<div class="advancement-criteria-wrapper">
  <div class="advancement-wrapper">
    <Advancement
      advancement={pair.value["statusName"]}
      asset_name={pair.value["assetName"]}
      status={pair.value["isComplete"] ? "complete" : pair.value["isPartial"] ? "partial" : "incomplete"}
    />
  </div>

  <div class="bar">
    <span class="label-counter">{count} / {total} ({Math.floor((count / total) * 100)}%)</span>
    <ProgressBar progress={count / total * 100} scale={1.0} width="15vw"/>
  </div>

  <div class={criteriaEntries.length > 20 ? "criteria-grid two-column" : "criteria-grid one-column"}>
    {#each criteriaEntries as [name, obj]}
      <div>
        <Criterion
          text={name}
          name={obj["filepath"]}
          isComplete={obj["isComplete"]}
        />
      </div>
    {/each}
  </div>
</div>