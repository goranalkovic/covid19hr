<script>
  import Summary from "./Summary.svelte";
  import CaseData from "./CaseData.svelte";
  import Rzero from "./Rzero.svelte";
  import Spinner from "./Spinner.svelte";
  import TableView from "./TableView.svelte";
  import Notice from "./Notice.svelte";
  import LogGraphs from "./LogGraphs.svelte";
  import { onMount } from "svelte";

  import TopAppBar, {
    Row,
    Section,
    Title,
    FixedAdjust,
    DenseFixedAdjust,
    ProminentFixedAdjust,
    ShortFixedAdjust,
  } from "@smui/top-app-bar";

  import { fade } from "svelte/transition/";

  let dateAxis,
    totalCases,
    recoveries,
    deaths,
    activeCases,
    totalCasesDelta,
    totalCasesR0,
    recoveriesDelta,
    deathsDelta,
    activeCasesDelta,
    summaryData,
    lastUpdate,
    r0avg,
    err = null;

  const round = (num, decimals = 3) => {
    return 1 * num.toFixed(decimals);
  };

  const calculateDeltas = (input) => {
    let output = [0];
    for (let i = 1; i < input.length; i++) {
      output.push(input[i] - input[i - 1]);
    }
    return output;
  };

  const calculateR0 = (input) => {
    let output = [0];
    for (let i = 1; i < input.length; i++) {
      if (input[i - 1] == 0) {
        output.push(0);
      } else {
        output.push(round(input[i] / input[i - 1]));
      }
    }
    return output;
  };

  const calcR0average = (input) => {
    let output = 0;
    for (let i of input) {
      output += i;
    }

    return output / input.length;
  };

  let loading = true;

  onMount(async () => {
    // Fetch data
    let response;
    try {
      response = await fetch(
        "https://api.jsonbin.io/b/5ea771be66e603359fdfd330/latest",
        {
          method: "GET",
          headers: {
            "secret-key":
              "$2b$10$iDj21mWHRqHDHxbScv4fR.0/.C2Iece5C4.eykPNV3jKXRMlxbBiO",
          },
        }
      );

      let json = await response.json();

      dateAxis = [];
      totalCases = [];
      recoveries = [];
      deaths = [];

      for (let record of json.data) {
        dateAxis.push(record.day);
        totalCases.push(record.total);
        recoveries.push(record.recovered);
        deaths.push(record.dead);
      }

      lastUpdate = json.lastUpdate;

      // Active cases
      activeCases = [];

      for (let i in dateAxis) {
        let value = totalCases[i] - recoveries[i] - deaths[i];
        activeCases.push(value);
      }

      // Deltas
      totalCasesDelta = [...calculateDeltas(totalCases)];
      recoveriesDelta = [...calculateDeltas(recoveries)];
      deathsDelta = [...calculateDeltas(deaths)];
      activeCasesDelta = [...calculateDeltas(activeCases)];

      // R0
      totalCasesR0 = [...calculateR0(totalCasesDelta)];

      // Summary
      summaryData = [
        activeCases.slice(-1).pop(),
        recoveries.slice(-1).pop(),
        deaths.slice(-1).pop(),
      ];

      // R0 average
      r0avg = calcR0average(totalCasesR0);

      // Ready!
      loading = false;
    } catch (error) {
      err = error;
    }
  });

  function timeout(ms) {
    return new Promise((resolve) => setTimeout(resolve, ms));
  }
</script>

<style>
  :global(h1, h2, h3, h4, h5) {
    text-align: center;
  }
</style>

<TopAppBar variant="fixed">
  <Row>
    <Section>
      <Title>COVID-19 podaci</Title>
    </Section>
    <Section align="end">

      {#if !loading}
        <p>
          Zadnje ažurirano
          <strong>{lastUpdate}</strong>
        </p>
      {/if}

    </Section>
  </Row>
</TopAppBar>

<main style="padding-top: 100px">

  {#if !loading}
    <div>

      <Summary input={summaryData} />

      <CaseData
        {dateAxis}
        {totalCases}
        {recoveries}
        {deaths}
        {activeCases}
        {totalCasesDelta}
        {deathsDelta} />

      <Rzero {dateAxis} input={totalCasesR0} {r0avg} />

      <LogGraphs {dateAxis} {totalCases} {recoveries} {deaths} {activeCases} />

      <TableView
        {totalCases}
        {totalCasesDelta}
        {totalCasesR0}
        {recoveries}
        {recoveriesDelta}
        {deaths}
        {deathsDelta}
        {activeCases}
        {activeCasesDelta}
        {dateAxis}
        {r0avg} />

      <Notice />
    </div>
  {:else if err}
    <div style="text-align: center">
      <h1>❌</h1>
      <h4>Dogodila se greška</h4>
      <p
        style="opacity: 0.5; margin-top: 1rem; font-size: 0.9em; font-family:
        monospace">
        {err}
      </p>
    </div>
  {:else}
    <Spinner />
  {/if}

  <footer style="margin: 2em auto; text-align: center">
    <small>
      Copyright
      <a href="https://goranalkovic.com">Goran Alković</a>
      2020
    </small>
  </footer>

</main>
