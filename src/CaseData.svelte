<script>
  import Chart from "svelte-frappe-charts";
  export let dateAxis = [];
  export let totalCases = [];
  export let recoveries = [];
  export let activeCases = [];
  export let deaths = [];

  let data = {
    labels: dateAxis,
    datasets: [
      {
        name: "Ukupno",
        values: totalCases
      },
      {
        name: "Oporavljeni",
        values: recoveries
      },
      {
        name: "Umrli",
        values: deaths
      },
      {
        name: "Aktivni",
        values: activeCases
      }
    ]
  };

  const axisOptions = {
    xIsSeries: true
  };

  const lineOptions = {
    regionFill: true,
    hideDots: true
  };

  const tooltipOptions = {
    formatTooltipX: d => (d + "").toUpperCase(),
    formatTooltipY: d => "Ukupno: " + d
  };

  const colors = [
    "dark-grey",
    "light-green",
    "red",
    "light-blue",
    "green",
    "violet",
    "yellow",
    "blue",
    "purple",
    "orange",
    "magenta",
    "light-grey"
  ];
</script>

<section>
  <h4>Pregled po danima</h4>
  <Chart {data} {axisOptions} {lineOptions} {colors} height="440" type="line" />
</section>
