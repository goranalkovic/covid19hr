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
      let dataSourceUrl = "https://www.koronavirus.hr/json/?action=podaci";
      // response = await fetch("https://www.koronavirus.hr/json/?action=podaci", {
      response = await fetch(
        `https://api.allorigins.win/get?url=${encodeURIComponent(
          dataSourceUrl
        )}`,
        {
          method: "GET",
          mode: "cors", // no-cors, *cors, same-origin
          credentials: "omit", // include, *same-origin, omit
          headers: {
            "Content-Type": "application/json; charset=utf-8",
          },
        }
      );

      console.log(response);

      let json = await response.json();

      json = json.reverse();

      json = [
        {
          SlucajeviSvijet: null,
          SlucajeviHrvatska: 1,
          UmrliSvijet: null,
          UmrliHrvatska: 0,
          IzlijeceniSvijet: null,
          IzlijeceniHrvatska: 0,
          Datum: "2020-02-25 08:00",
        },
        ...json,
      ];

      dateAxis = [];
      totalCases = [];
      recoveries = [];
      deaths = [];

      for (let record of json) {
        // dateAxis.push(record.day);
        // totalCases.push(record.total);
        // recoveries.push(record.recovered);
        // deaths.push(record.dead);

        let day = new Date(record.Datum);
        let total = record.SlucajeviHrvatska;
        let recovered = record.IzlijeceniHrvatska;
        let dead = record.UmrliHrvatska;

        dateAxis.push(day);
        totalCases.push(total);
        recoveries.push(recovered);
        deaths.push(dead);
      }

      lastUpdate = new Date(json[json.length - 1].Datum);

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
        <p style="padding-right: 20px; display: flex; align-items: center;">
          <i class="material-icons topbar-icon">refresh</i>

          <span style="margin-left: 6px">
            {new Intl.DateTimeFormat('hr', {
              month: 'narrow',
              day: 'numeric',
              hour: 'numeric',
              minute: 'numeric',
            }).format(lastUpdate)}
          </span>

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
        {activeCasesDelta}
        {deathsDelta} />

      <Rzero {dateAxis} input={totalCasesR0} {r0avg} />

      <!-- <LogGraphs {dateAxis} {totalCases} {recoveries} {deaths} {activeCases} /> -->

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

  <footer
    style="margin: 2rem auto; padding: 2rem 0; text-align: center; background:
    transparent; font-size: 1rem;">

    <a
      style="text-decoration: none; font-size: 1rem"
      href="https://goranalkovic.com">
      Copyright Goran Alković 2020
    </a>

  </footer>

</main>
