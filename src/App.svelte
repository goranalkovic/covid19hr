<script>
  import Summary from "./Summary.svelte";
  import CaseData from "./CaseData.svelte";
  import Rzero from "./Rzero.svelte";
  import { onMount } from "svelte";
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
    lastUpdate;

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
    let response = await fetch("data.json");
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
      <p style="text-align: center; opacity: 0.6;">
        Zadnje ažurirano
        <strong>{lastUpdate}</strong>
      </p>

      <Summary input={summaryData} />
      <CaseData
        {dateAxis}
        {totalCases}
        {recoveries}
        {deaths}
        {activeCases}
        {totalCasesDelta}
        {deathsDelta} />
      <Rzero {dateAxis} input={totalCasesR0} />

      <section style="max-width: 400px; text-align: center; margin: 2em auto;">
        <h4>
          <span style="color: orange;">⚠</span>
          Napomena
        </h4>
        <br />
        <p>
          Ovi su podaci prikupljeni prema informacijama sa sjednica Nacionalnog
          stožera, pa je moguće da se u prijenosu podataka pojavila greška.
        </p>
        <p>
          Potpuno službene i provjerene podatke uvijek možete dobiti na
          službenoj stranici
          <a href="https://koronavirus.hr">koronavirus.hr</a>

        </p>
        <br />
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

  <footer style="margin: 2em auto;">
    <p>
      Copyright
      <a href="https://goranalkovic.com">Goran Alković</a>
      , 2020
    </p>
  </footer>

</main>
