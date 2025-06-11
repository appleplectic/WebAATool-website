<script>
  import TotalProgress from "./lib/TotalProgress.svelte";
  import Advancement from "./lib/Advancement.svelte";
  import AdvancementWithCriteria from "./lib/AdvancementWithCriteria.svelte";

  let url = "";
  let isConnected = false;
  let source;
  let data;

  function connectToServer() {
    if (!url) return;

    source = new EventSource(url);
    isConnected = true;

    source.onmessage = async event => {
      const cleaned = event.data
              .replace(/\u0000/g, ' ')
              .replace(/\uFFFD/g, ' ')
              .replace(/[\x00-\x1F\x7F-\x9F]/g, ' ')
              .replace(/[\u2028\u2029]/g, ' ')
              .replace(/\uFEFF/g, ' ')
              .replace(/[\u200B-\u200D\uFEFF]/g, '');

      data = JSON.parse(cleaned);
    };
  }


  $: advancements = data ? Object.entries(data["advancements"] ?? {}) : [];

  let nocriteria = [];
  let criteria = [];
  let count = 0;
  let username = "";
  let tracking = "Currently not tracking...";
  let IGT = "";
  let total = 80;

  $: if (data) {
    nocriteria = [];
    criteria = [];
    count = 0;
    IGT = data["IGT"];
    total = advancements.length;

    for (const [key, value] of advancements) {
      if (value["isComplete"] === true) {
        count++;
      }

      if (value["hasCriteria"] === true) {
        criteria.push({key, value});
      } else {
        nocriteria.push({key, value});
      }
    }

    if (data["worldName"] !== "") {
      tracking = "Tracking " + data["worldName"];
    }

    username = data["username"];
  }

  $: heading = data
          ? (username === ""
                  ? `${data["category"]} Tracker`
                  : `${username}'s ${data["version"]} ${data["category"]} Progress`)
          : "All Advancements Tracker";

  $: title = username === ""
          ? "Web AATool"
          : `${count}/${total} | ${IGT} IGT | ${username}`;

  let notch_apple_status, netherite_status, trident_status, shell_status, skull_status, beacon_status, bee_status = "incomplete";
  let notch_apple_label = "Obtain God Apple";
  let netherite_label = "Debris: 0\nTNT: 0";
  let trident_label = "Obtain Trident";
  let shell_label = "Shells 0 / 8";
  let skull_label = "Skulls 0 / 3";
  let beacon_label = "Gold Blocks 0 / 164";
  let bee_label = "0 Hives Collected";
  $: if (data) {
    notch_apple_status = data["complexObjectives"]["egap"]["isComplete"]
            ? "complete"
            : data["complexObjectives"]["egap"]["isPartiallyComplete"]
                    ? "partial"
                    : "incomplete";
    notch_apple_label = data["complexObjectives"]["egap"]["statusName"].replace("\u0000", " ");
    netherite_status = data["complexObjectives"]["netherite"]["isComplete"]
            ? "complete"
            : data["complexObjectives"]["netherite"]["isPartiallyComplete"]
                    ? "partial"
                    : "incomplete";
    netherite_label = data["complexObjectives"]["netherite"]["statusName"].replaceAll(":", ": ");
    trident_status = data["complexObjectives"]["trident"]["isComplete"]
            ? "complete"
            : data["complexObjectives"]["trident"]["isPartiallyComplete"]
                    ? "partial"
                    : "incomplete";
    trident_label = data["complexObjectives"]["trident"]["statusName"];
    shell_status = data["complexObjectives"]["nautilusshells"]["isComplete"]
            ? "complete"
            : data["complexObjectives"]["nautilusshells"]["isPartiallyComplete"]
                    ? "partial"
                    : "incomplete";
    shell_label = data["complexObjectives"]["nautilusshells"]["statusName"].replaceAll("\u0000", " ");
    skull_status = data["complexObjectives"]["witherskulls"]["isComplete"]
            ? "complete"
            : data["complexObjectives"]["witherskulls"]["isPartiallyComplete"]
                    ? "partial"
                    : "incomplete";
    skull_label = data["complexObjectives"]["witherskulls"]["statusName"].replaceAll("\u0000", " ");
    beacon_status = data["complexObjectives"]["goldblocks"]["isComplete"]
            ? "complete"
            : data["complexObjectives"]["goldblocks"]["isPartiallyComplete"]
                    ? "partial"
                    : "incomplete";
    beacon_label = data["complexObjectives"]["goldblocks"]["statusName"].replaceAll("\u0000", " ");
    bee_status = data["complexObjectives"]["bees"]["isComplete"]
            ? "complete"
            : data["complexObjectives"]["bees"]["isPartiallyComplete"]
                    ? "partial"
                    : "incomplete";
    bee_label = data["complexObjectives"]["bees"]["statusName"].replaceAll("\u0000", " ");
  }
</script>

<style>
  .centered {
    max-width: fit-content;
    margin-left: auto;
    margin-right: auto;
  }

  .advancement-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(80px, 1fr));
    justify-items: center;
    margin-top: 8px;
  }

  .advancement-grid-criteria {
    display: grid;
    grid-template-columns: repeat(6, 1fr);
    justify-items: center;
    margin-top: 15px;
  }

  .grid-wrapper {
    display: flex;
    align-items: flex-start;
    gap: 5px;
  }

  .advancement-grid {
    flex: 1;
  }

  .side-column {
    width: 100px;
  }
</style>

<svelte:head>
  <title>{title}</title>
</svelte:head>

{#if !isConnected}
  <h1 style="text-align: center;">Web AATool</h1>
  <h3 style="text-align: center;">Created by Appleplectic, made possible by CTM's original AATool.</h3>
  <div class="centered" style="text-align: center; margin-top: 40px;">
    <input
      type="text"
      placeholder="Enter server URL (http://example.com/sse)"
      bind:value={url}
      style="font-family: Minecraft; padding: 8px; font-size: 0.9em; width: 320px; border: 1px solid #ccc; border-radius: 6px;"
    />
    <button
      on:click={connectToServer}
      style="font-family: Minecraft; margin-left: 8px; padding: 8px 16px; font-size: 1em; border: none; background-color: #8c784d; color: white; border-radius: 6px; cursor: pointer;"
    >
      Connect
    </button>
  </div>
{:else}
<main>
  <div class="centered">
    <h1 style="text-align: center;">{heading}</h1>
    <TotalProgress numCompleted={count} numTotal={total} {IGT}/>
    <div class="centered" style="font-size: 0.75em; margin-bottom: 12px;">{tracking}</div>
  </div>

  <div class="grid-wrapper">
    <div class="advancement-grid">
      {#each nocriteria as pair}
        <Advancement
          status={pair.value["isComplete"] ? "complete" : pair.value["isPartial"] ? "partial" : "incomplete"}
          advancement={pair.value["statusName"]}
          asset_name={pair.value["assetName"]}
        />
      {/each}
    </div>

    <div class="side-column">
      <div class="advancement-grid">
        <Advancement
          status={notch_apple_status}
          advancement={notch_apple_label}
          asset_name="enchanted_golden_apple.gif"
        />
        <Advancement
          status={netherite_status}
          advancement={netherite_label}
          asset_name="obtain_ancient_debris.png"
        />
        <Advancement
          status={trident_status}
          advancement={trident_label}
          asset_name="throw_trident.png" />
        <Advancement
          status={shell_status}
          advancement={shell_label}
          asset_name="nautilus_shell.png" />
        <Advancement
          status={skull_status}
          advancement={skull_label}
          asset_name="get_wither_skull.png" />
        <Advancement
          status={beacon_status}
          advancement={beacon_label}
          asset_name="gold_block.png" />
        <Advancement
          status={bee_status}
          advancement={bee_label}
          asset_name="silk_touch_nest.png" />
      </div>
    </div>
  </div>

  <div class="advancement-grid-criteria">
    {#each criteria as pair}
      <AdvancementWithCriteria {pair}/>
    {/each}
  </div>
</main>
{/if}