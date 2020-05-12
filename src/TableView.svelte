<script>
  import DataTable, { Head, Body, Row, Cell } from "@smui/data-table";
  import Button, { Group, GroupItem, Label } from "@smui/button";
  import IconButton, { Icon } from "@smui/icon-button";
  import { onMount } from "svelte";

  export let totalCases;
  export let totalCasesDelta;
  export let totalCasesR0;
  export let recoveries;
  export let recoveriesDelta;
  export let deaths;
  export let deathsDelta;
  export let activeCases;
  export let activeCasesDelta;
  export let dateAxis;
  export let r0avg;

  let currentPage = 1;
  let pageSize = 10;

  const round = (num, dec = 3) => {
    return num.toFixed(dec);
  };

  const average = (v) => {
    let total = 0;
    for (let i of v) {
      total += i;
    }

    let result = round(total / v.length, 0);
    return result > 0 ? "+" + result : result;
  };

  function range(start, count) {
    return Array.apply(0, Array(count)).map((element, index) => index + start);
  }

  $: paginationSteps = Math.ceil(totalCases.length / pageSize);
  $: paginationArray = [...Array(paginationSteps).keys()].map((x) => ++x);
  $: dataIndices = range((currentPage - 1) * pageSize, pageSize);

  let tableWidth = "90vw";

  function myFunction(x) {
    if (x.matches) {
      tableWidth = "90vw";
    } else {
      tableWidth = "60vw";
    }
  }

  onMount(() => {
    var x = window.matchMedia("(max-width: 900px)");
    myFunction(x); // Call listener function at run time
    x.addListener(myFunction);
  });
</script>

<style>
  section {
    width: 90vw;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    align-items: center;
    max-width: 1400px;
  }
</style>

<section>
  <h1>Tablični prikaz</h1>
  <br />

  <DataTable
    style="min-width: 400px; width: {tableWidth}; max-width: 800px;"
    table$aria-label="Tablični prikaz">
    <Head>
      <Row>
        <Cell>Dan</Cell>
        <Cell>Datum</Cell>
        <Cell>Ukupno</Cell>
        <Cell>R₀</Cell>
        <Cell>Oporavljeni</Cell>
        <Cell>Umrli</Cell>
        <Cell>Aktivni</Cell>
      </Row>
    </Head>
    <Body>
      {#each dataIndices as i}
        {#if totalCases[i] != null}
          <Row>
            <Cell>{i + 1}</Cell>
            <Cell>{dateAxis[i]}</Cell>
            <Cell>
              {totalCases[i]}
              {#if totalCasesDelta[i] != 0}
                <small style="opacity: 0.6;">+{totalCasesDelta[i]}</small>
              {/if}
            </Cell>
            <Cell>
              {@html totalCasesR0[i] > 0 ? totalCasesR0[i] : '<small>-</small>'}
            </Cell>
            <Cell>
              {recoveries[i]}
              {#if recoveriesDelta[i] != 0}
                <small style="opacity: 0.6;">+{recoveriesDelta[i]}</small>
              {/if}
            </Cell>
            <Cell>
              {deaths[i]}
              {#if deathsDelta[i] != 0}
                <small style="opacity: 0.6;">+{deathsDelta[i]}</small>
              {/if}
            </Cell>
            <Cell>
              {activeCases[i]}
              {#if activeCasesDelta[i] != 0}
                <small style="opacity: 0.6;">
                  {activeCasesDelta[i] < 0 ? '' : '+'}{activeCasesDelta[i]}
                </small>
              {/if}
            </Cell>
          </Row>
        {/if}
      {/each}
      <Row>
        <Cell colspan="2">
          <strong>Prosjek po danu</strong>
        </Cell>
        <!-- <Cell /> -->
        <Cell>{average(totalCasesDelta)}</Cell>
        <Cell>{round(r0avg)}</Cell>
        <Cell>{average(recoveriesDelta)}</Cell>
        <Cell>{average(deathsDelta)}</Cell>
        <Cell>{average(activeCasesDelta)}</Cell>
      </Row>
      <Row>
        <Cell colspan="4" />

        <Cell colspan="2" style="text-align: right">
          <small>Stranica</small>
          {currentPage}
          <small>od</small>
          {paginationSteps}
        </Cell>

        <Cell>
          <IconButton disabled={currentPage < 2} on:click={() => currentPage--}>
            <svg
              xmlns="http://www.w3.org/2000/svg"
              height="24"
              viewBox="0 0 24 24"
              width="24">
              <path d="M0 0h24v24H0V0z" fill="none" />
              <path
                d="M15.41 7.41L14 6l-6 6 6 6 1.41-1.41L10.83 12l4.58-4.59z" />
            </svg>
          </IconButton>
          <IconButton
            disabled={currentPage > paginationSteps - 1}
            on:click={() => currentPage++}>
            <svg
              xmlns="http://www.w3.org/2000/svg"
              height="24"
              viewBox="0 0 24 24"
              width="24">
              <path d="M0 0h24v24H0V0z" fill="none" />
              <path d="M10 6L8.59 7.41 13.17 12l-4.58 4.59L10 18l6-6-6-6z" />
            </svg>
          </IconButton>
        </Cell>
      </Row>
    </Body>
  </DataTable>

  <div style="display: block; height: 20px" />

</section>
