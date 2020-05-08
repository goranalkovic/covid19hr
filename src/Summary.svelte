<script>
  import Chart from "svelte-frappe-charts";
  import Card, {
    Content,
    PrimaryAction,
    Media,
    MediaContent,
    Actions,
    ActionButtons,
    ActionIcons,
  } from "@smui/card";

  export let input;

  let data = {
    labels: ["Aktivni", "Oporavljeni", "Umrli"],
    datasets: [
      {
        values: input,
      },
    ],
  };

  const colors = ["light-blue", "light-green", "red"];

  const total = (input) => input.reduce((a, b) => a + b, 0);
</script>

<style>
  .container {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    justify-content: center;
    align-items: stretch;
    align-content: center;
  }
</style>

<section style="display: flex; flex-direction: column; align-items: center">
  <div class="container">
    <Card style="width: 240px; margin: 10px;" variant="outlined" padded>
      <span
        class="mdc-typography--overline mdc-theme--primary"
        style="color: rgb(184, 194, 204)">
        Ukupno
      </span>
      <span class="mdc-typography--headline3">{total(input)}</span>
    </Card>
    <Card style="width: 240px; margin: 10px;" variant="outlined" padded>
      <div class="mdc-typography--overline" style="color: rgb(99, 189, 228)">
        Aktivni
      </div>
      <div class="mdc-typography--headline3">{input[0]}</div>
    </Card>
    <Card style="width: 240px; margin: 10px;" variant="outlined" padded>
      <div class="mdc-typography--overline" style="color: rgb(152, 216, 91)">
        Oporavljeni
      </div>
      <div class="mdc-typography--headline3">{input[1]}</div>
    </Card>
    <Card style="width: 240px; margin: 10px;" variant="outlined" padded>
      <div class="mdc-typography--overline" style="color: rgb(230, 63, 63)">
        Umrli
      </div>
      <div class="mdc-typography--headline3">{input[2]}</div>
    </Card>

  </div>

  <div style="margin: 10px; width: 80vw; max-width: 1000px">
    <Chart {data} {colors} height="140" type="percentage" />
  </div>
</section>
