<script lang="ts">
  import Solution from "./components/Solution.svelte";
  import seedrandom from "seedrandom";

  const now = new Date(Date.now());
  const today = Date.UTC(now.getFullYear(), now.getMonth(), now.getDate());

  let daily = true;

  function randomInt(min: number, max: number): number {
    return Math.floor(Math.random() * (max - min + 1)) + min;
  }
  function getRandomDate(minYear: number, maxYear: number): Date {
    if (daily) {
      seedrandom(today, { global: true });
    }
    let year = randomInt(minYear, maxYear);
    let month = randomInt(0, 11);
    let maxday = new Date(year, month + 1, 0).getDate();
    let day = randomInt(1, maxday);
    return new Date(Date.UTC(year, month, day));
  }
  let minYear = 1000;
  let maxYear = 3000;
  let date = getRandomDate(minYear, maxYear);
  let showsolution = false;
</script>

<main class="flex justify-center p-20">
  <div class="prose wrapper flex flex-col justify-self-center">
    <div class="flex flex-row toolbar absolute top-2 left-2">
      <div class="form-control">
        <label class="label cursor-pointer">
          <span class="label-text mr-3">Daily Mode</span>
          <input
            type="checkbox"
            class="toggle"
            bind:checked={daily}
            on:change={() => {
              minYear = 1000;
              maxYear = 3000;
              date = getRandomDate(minYear, maxYear);
            }}
          />
        </label>
      </div>
    </div>
    {#if !daily}
      <div class="flex flex-row justify-center items-end toolbar gap-6">
        <div class="flex flex-col">
          <label for="minYear">minYear</label>
          <input
            bind:value={minYear}
            id="minYear"
            type="number"
            placeholder="minYear"
            class="input input-sm input-bordered no-spin"
            label="Min Year"
          />
        </div>
        <div class="flex flex-col">
          <label for="minYear">maxYear</label>
          <input
            bind:value={maxYear}
            id="maxYear"
            type="number"
            placeholder="maxYear"
            class="input input-sm input-bordered no-spin"
            label="Min Year"
          />
        </div>
        <button
          class="btn"
          on:click={() => {
            date = getRandomDate(minYear, maxYear);
            showsolution = false;
          }}>Reroll</button
        >
      </div>
    {/if}
    <div class="flex flex-row justify-center mt-20">
      <h1 class="date">
        <!-- {date.toDateString()} -->
        {date.getDate() +
          ". " +
          (date.getMonth() + 1) +
          ". " +
          date.getFullYear()}
      </h1>
    </div>
    <button
      class="btn"
      on:click={() => {
        showsolution = !showsolution;
      }}
    >
      {showsolution ? "Hide solution" : "Show solution"}
    </button>
    <div class="mt-20">
      {#if showsolution}
        <Solution randomDate={date} />
      {/if}
    </div>
  </div>
</main>

<style>
  .no-spin::-webkit-inner-spin-button,
  .no-spin::-webkit-outer-spin-button {
    -webkit-appearance: none !important;
    margin: 0 !important;
  }

  .no-spin {
    -moz-appearance: textfield !important;
  }
</style>
