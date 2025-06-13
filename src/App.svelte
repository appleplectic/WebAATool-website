<script>
  import {onDestroy, onMount} from 'svelte';

  import TotalProgress from "./lib/TotalProgress.svelte";
  import Advancement from "./lib/Advancement.svelte";
  import AdvancementWithCriteria from "./lib/AdvancementWithCriteria.svelte";

  import {EventSourcePolyfill} from 'event-source-polyfill';
  import {BASE_PATH} from "./lib/config.js";

  let source;
  let data;
  let url = ""
  let isConnected = false;
  let connecting = false;
  let nocriteria = [];
  let criteria = [];
  let count = 0;
  let username = "";
  let tracking = "Currently not tracking...";
  let IGT = "";
  let total = 80;

  onMount(() => {
    const params = new URLSearchParams(window.location.search);
    const serverParam = params.get('server');
    if (serverParam) {
      url = serverParam;
      connectToServer();
    }
  });

  onDestroy(() => {
    if (source) source.close();
  });

  function clean(str) {
    return str.replace(/\u0000/g, ' ')
            .replace(/\uFFFD/g, ' ')
            .replace(/[\x00-\x1F\x7F-\x9F]/g, ' ')
            .replace(/[\u2028\u2029]/g, ' ')
            .replace(/\uFEFF/g, ' ')
            .replace(/[\u200B-\u200D\uFEFF]/g, '');
  }

  async function connectToServer() {
    connecting = true;
    if (!url) return;

    url = url.replace(/\/$/, '');

    try {
      source = new EventSourcePolyfill(url, {
        headers: { "Bypass-Tunnel-Reminder": "true" }
      });

      source.onmessage = async event => {
        try {
          const cleaned = clean(event.data);
          data = JSON.parse(cleaned);
          isConnected = true;
          connecting = false;
          const newUrl = `${window.location.origin}${window.location.pathname}?server=${encodeURIComponent(url)}`;
          window.history.replaceState(null, "", newUrl);
        } catch (parseError) {
          console.error("Failed to parse event data:", parseError);
          alert("Received invalid data from server. Check the server output.");
          source.close();
        }
      };

      source.onerror = (err) => {
        console.error("Connection error:", err);
        alert("Failed to connect to server. Please check the URL and try again.");
        source.close();
        connecting = false;
      };
    } catch (err) {
      console.error("Unexpected error:", err);
      alert("Unexpected error occurred. See console for details.");
    }
  }

  async function sampleBtn() {
    try {
      const res = await fetch(BASE_PATH + 'sample-data.json');
      if (!res.ok) throw new Error(`Failed to fetch: ${res.status}`);
      const raw = await res.text();
      data = JSON.parse(clean(raw));
      isConnected = true;
    } catch (e) {
      console.error(e);
    }
  }

  function refreshPage() {
    window.location.href = window.location.origin + window.location.pathname;
  }

  $: advancements_us = data ? Object.entries(data["advancements"] ?? {}) : [];
  $: advancements = data ? Object.entries(Object.fromEntries(
          advancements_us.sort(([, a], [, b]) => a["order"] - b["order"])
  )) : [];

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
                  ? `${data["version"]} ${data["category"]} Tracker`
                  : `${username}'s ${data["version"]} ${data["category"]} Progress`)
          : "All Advancements Tracker";

  $: title = data
          ? (username === ""
              ? `${count}/${total} | ${IGT} IGT`
              : `${count}/${total} | ${IGT} IGT | ${username}`)
        : "Web AATool";

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
                    ? "incomplete" // partial not working
                    : "incomplete";
    notch_apple_label = data["complexObjectives"]["egap"]["statusName"].replace("\u0000", " ");
    netherite_status = data["complexObjectives"]["netherite"]["isComplete"]
            ? "complete"
            : data["complexObjectives"]["netherite"]["isPartiallyComplete"]
                    ? "incomplete"
                    : "incomplete";
    netherite_label = data["complexObjectives"]["netherite"]["statusName"].replaceAll(":", ": ");
    trident_status = data["complexObjectives"]["trident"]["isComplete"]
            ? "complete"
            : data["complexObjectives"]["trident"]["isPartiallyComplete"]
                    ? "incomplete"
                    : "incomplete";
    trident_label = data["complexObjectives"]["trident"]["statusName"];
    shell_status = data["complexObjectives"]["nautilusshells"]["isComplete"]
            ? "complete"
            : data["complexObjectives"]["nautilusshells"]["isPartiallyComplete"]
                    ? "incomplete"
                    : "incomplete";
    shell_label = data["complexObjectives"]["nautilusshells"]["statusName"].replaceAll("\u0000", " ");
    skull_status = data["complexObjectives"]["witherskulls"]["isComplete"]
            ? "complete"
            : data["complexObjectives"]["witherskulls"]["isPartiallyComplete"]
                    ? "incomplete"
                    : "incomplete";
    skull_label = data["complexObjectives"]["witherskulls"]["statusName"].replaceAll("\u0000", " ");
    beacon_status = data["complexObjectives"]["goldblocks"]["isComplete"]
            ? "complete"
            : data["complexObjectives"]["goldblocks"]["isPartiallyComplete"]
                    ? "incomplete"
                    : "incomplete";
    beacon_label = data["complexObjectives"]["goldblocks"]["statusName"].replaceAll("\u0000", " ");
    bee_status = data["complexObjectives"]["bees"]["isComplete"]
            ? "complete"
            : data["complexObjectives"]["bees"]["isPartiallyComplete"]
                    ? "incomplete"
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
    width: 100%;
  }

  .advancement-grid-s {
    display: grid;
    justify-items: center;
    margin-top: 10vh;
    width: 100%;
    gap: 10px;
  }

  .advancement-grid-criteria {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
    justify-items: center;
    margin-top: 15px;
    gap: 8px;
  }

  .grid-wrapper {
    display: flex;
    align-items: center;
    gap: 5px;
  }

  .advancement-grid {
    flex: 1;
  }

  .side-column {
    width: 100px;
  }

  .advancement-section {
    width: 100%;
  }

  .advancement-sections-column {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 24px;
  }

  .split {
    font-size: 1.25em;
    margin-left: 16px;
  }

  .connect-btn {
    font-family: Minecraft, monospace;
    margin-left: 8px;
    padding: 8px 16px;
    font-size: 1em;
    border: none;
    background-color: #8c784d;
    color: white;
    border-radius: 6px;
    cursor: pointer;
  }

  .connect-btn:disabled {
    background-color: #a9a9a9;
    cursor: not-allowed;
    color: #eee;
  }

  .input-box {
    font-family: Minecraft, monospace;
    padding: 8px;
    font-size: 0.9em;
    width: 320px;
    border: 1px solid #ccc;
    border-radius: 6px;
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
      placeholder="Enter server URL (https://example.com/)"
      bind:value={url}
      on:keydown={(e) => { if (e.key === 'Enter') connectToServer(); }}
      class="input-box"
    />
    <button
      class="connect-btn"
      on:click={connectToServer}
      disabled={connecting}
    >
      {connecting ? "Connecting..." : "Connect"}
    </button>
    <button
      class="connect-btn"
      on:click={sampleBtn}
    >
      Sample
    </button>
  </div>
{:else}
<main>
  <div class="centered">
    <a href="/" style="text-decoration:none; color:inherit;" on:click={refreshPage}>
      <h1 style="text-align: center; cursor: pointer;">{heading}</h1>
    </a>
    <TotalProgress numCompleted={count} numTotal={total} {IGT}/>
    <div class="centered" style="font-size: 0.75em; margin-bottom: 12px;">{tracking}</div>
  </div>

  <div class="grid-wrapper">
    <div class="advancement-sections-column">
      <div class="advancement-section">
        <div class="split">Any%</div>
        <div class="advancement-grid">
          {#each nocriteria.slice(0, 35) as pair}
            <Advancement
              status={pair.value["isComplete"] ? "complete" : pair.value["isPartial"] ? "partial" : "incomplete"}
              advancement={pair.value["statusName"]}
              asset_name={pair.value["assetName"]}
            />
          {/each}
        </div>
      </div>

      <div class="advancement-section">
        <div class="split">Midgame</div>
        <div class="advancement-grid">
          {#each nocriteria.slice(35, 58) as pair}
            <Advancement
                    status={pair.value["isComplete"] ? "complete" : pair.value["isPartial"] ? "partial" : "incomplete"}
                    advancement={pair.value["statusName"]}
                    asset_name={pair.value["assetName"]}
            />
          {/each}
        </div>
      </div>

      <div class="advancement-section">
        <div class="split">Endgame</div>
        <div class="advancement-grid">
          {#each nocriteria.slice(58, 74) as pair}
            <Advancement
                    status={pair.value["isComplete"] ? "complete" : pair.value["isPartial"] ? "partial" : "incomplete"}
                    advancement={pair.value["statusName"]}
                    asset_name={pair.value["assetName"]}
            />
          {/each}
        </div>
      </div>
    </div>

    <div class="side-column">
      <div class="advancement-grid-s">
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