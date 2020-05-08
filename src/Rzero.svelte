<script>
  import Chart from "svelte-frappe-charts";
  export let dateAxis = [];
  export let input = [];
  export let r0avg = 1.1;

  let data = {
    labels: dateAxis,
    datasets: [
      {
        values: [...input],
      },
    ],
    yMarkers: [
      {
        label: "Prosječna vrijednost",
        value: r0avg,
        options: { labelPos: "left" },
      },
      {
        label: "Još OK",
        value: 1,
      },
      {
        label: "Nije optimalno",
        value: 2,
      },
    ],
  };

  const axisOptions = {
    xIsSeries: true,
  };

  const lineOptions = {
    regionFill: true,
    hideDots: true,
  };

  const tooltipOptions = {
    //   formatTooltipX: d => (d + "").toUpperCase(),
    formatTooltipY: (d) => "R₀ = " + d,
  };

  const colors = ["purple"];
</script>

<style>
  section {
    width: 90vw;
    margin: 0 auto;
    max-width: 1400px;
  }
</style>

<section>
  <h1>R₀</h1>
  <Chart {data} {axisOptions} {lineOptions} {colors} height="440" type="line" />
</section>
