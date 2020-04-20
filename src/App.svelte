<script>
  import Summary from "./Summary.svelte";
  import CaseData from "./CaseData.svelte";
  import Rzero from "./Rzero.svelte";
  import { onMount } from "svelte";
  import { fade } from "svelte/transition/";

  let dateAxis = [
    "25.2.",
    "26.2.",
    "27.2.",
    "28.2.",
    "29.2.",
    "1.3.",
    "2.3.",
    "3.3.",
    "4.3.",
    "5.3.",
    "6.3.",
    "7.3.",
    "8.3.",
    "9.3.",
    "10.3.",
    "11.3.",
    "12.3.",
    "13.3.",
    "14.3.",
    "15.3.",
    "16.3.",
    "17.3.",
    "18.3.",
    "19.3.",
    "20.3.",
    "21.3.",
    "22.3.",
    "23.3.",
    "24.3.",
    "25.3.",
    "26.3.",
    "27.3.",
    "28.3.",
    "29.3.",
    "30.3.",
    "31.3.",
    "1.4.",
    "2.4.",
    "3.4.",
    "4.4.",
    "5.4.",
    "6.4.",
    "7.4.",
    "8.4.",
    "9.4.",
    "10.4.",
    "11.4.",
    "12.4.",
    "13.4.",
    "14.4.",
    "15.4.",
    "16.4.",
    "17.4.",
    "18.4.",
    "19.4.",
    "20.4."
  ];
  let totalCases = [
    1,
    2,
    3,
    3,
    5,
    7,
    8,
    9,
    9,
    10,
    10,
    11,
    12,
    12,
    13,
    16,
    19,
    32,
    37,
    49,
    57,
    69,
    89,
    105,
    128,
    206,
    254,
    315,
    382,
    442,
    495,
    586,
    657,
    713,
    790,
    867,
    963,
    1011,
    1079,
    1126,
    1182,
    1222,
    1282,
    1343,
    1407,
    1495,
    1534,
    1600,
    1650,
    1704,
    1741,
    1791,
    1814,
    1832,
    1871,
    1881
  ];
  let recoveries = [
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    1,
    1,
    2,
    3,
    4,
    5,
    5,
    5,
    5,
    5,
    5,
    5,
    16,
    22,
    22,
    37,
    49,
    52,
    64,
    67,
    73,
    88,
    92,
    119,
    125,
    130,
    167,
    179,
    219,
    231,
    323,
    373,
    400,
    415,
    473,
    529,
    600,
    615,
    709,
    771
  ];
  let deaths = [
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    0,
    1,
    1,
    1,
    1,
    1,
    1,
    1,
    3,
    3,
    5,
    6,
    6,
    6,
    6,
    7,
    8,
    12,
    15,
    16,
    18,
    19,
    20,
    21,
    21,
    23,
    25,
    31,
    33,
    35,
    36,
    39,
    47,
    47
  ];
  let activeCases = [];
  let totalCasesDelta = [];
  let totalCasesR0 = [];
  let recoveriesDelta = [];
  let deathsDelta = [];
  let activeCasesDelta = [];
  let summaryData = [];

  let lastUpdate = "20.4.2020.";

  const round = num => {
    return +(Math.round(num + "e+3") + "e-3");
  };

  const calculateDeltas = input => {
    let output = [0];
    for (let i = 1; i < input.length; i++) {
      output.push(input[i] - input[i - 1]);
    }
    return output;
  };

  const calculateR0 = input => {
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

  let loading = true;

  onMount(async () => {
    // Fetch data

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
      deaths.slice(-1).pop()
    ];

    // Ready!
    loading = false;
  });

  function timeout(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
  }
</script>

<style>
  :global(h1, h2, h3, h4, h5) {
    text-align: center;
  }
</style>

<main>
  <h1>COVID-19 podaci</h1>

  {#if !loading}
    <div transition:fade>
      <p>
        Zadnje ažurirano
        <strong>{lastUpdate}</strong>
      </p>

      <Summary input={summaryData} />
      <CaseData {dateAxis} {totalCases} {recoveries} {deaths} {activeCases} />
      <Rzero {dateAxis} input={totalCasesR0} />

      <section>
        <a href="https://koronavirus.hr">Visit the official government site</a>
      </section>

      <section>
        <h4>Tablični prikaz</h4>
        <br />

        <table>
          <thead>
            <tr>
              <th>Dan</th>
              <th>Datum</th>
              <th>Ukupno slučajeva</th>
              <th>
                R
                <sub>0</sub>
              </th>
              <th>Oporavljenih</th>
              <th>Umrlih</th>
              <th>Aktivnih slučajeva</th>
            </tr>
          </thead>
          <tbody>
            {#each totalCases as currentCase, i}
              <tr>
                <td>{i + 1}</td>
                <td>{dateAxis[i]}</td>
                <td>
                  {totalCases[i]}
                  {#if totalCasesDelta[i] != 0}(+{totalCasesDelta[i]}){/if}
                </td>
                <td>{totalCasesR0[i]}</td>
                <td>
                  {recoveries[i]}
                  {#if recoveriesDelta[i] != 0}(+{recoveriesDelta[i]}){/if}
                </td>
                <td>
                  {deaths[i]}
                  {#if deathsDelta[i] != 0}(+{deathsDelta[i]}){/if}
                </td>
                <td>
                  {activeCases[i]}
                  {#if activeCasesDelta[i] != 0}
                    ({activeCasesDelta[i] < 0 ? '' : '+'}{activeCasesDelta[i]})
                  {/if}
                </td>
              </tr>
            {/each}
          </tbody>
        </table>
      </section>
    </div>
  {:else}Dohvaćam podatke...{/if}

  <footer style="margin-top: 3em">

    <p>Podaci ručno prikupljeni sa sjednica Nacionalnog stožera</p>
    <br />
    <p>
      Copyright
      <a href="https://goranalkovic.com">Goran Alković</a>
      , 2020
    </p>
  </footer>

</main>
