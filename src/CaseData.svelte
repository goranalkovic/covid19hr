<script>
  import Chart from "svelte-frappe-charts";
  export let dateAxis = [];
  export let totalCases = [];
  export let totalCasesDelta = [];
  export let deathsDelta = [];
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

  let data2 = {
    labels: dateAxis,
    datasets: [
      { name: "Novi slučajevi", values: totalCasesDelta },
      { name: "Nove smrti", values: deathsDelta }
    ]
  };

  let data3 = {
    labels: dateAxis.slice(-7),
    datasets: [
      {
        name: "Ukupno",
        values: totalCases.slice(-7)
      },
      {
        name: "Oporavljeni",
        values: recoveries.slice(-7)
      },
      {
        name: "Umrli",
        values: deaths.slice(-7)
      },
      {
        name: "Aktivni",
        values: activeCases.slice(-7)
      }
    ]
  };

  let data4 = {
    labels: dateAxis.slice(-14),
    datasets: [
      {
        name: "Ukupno",
        values: totalCases.slice(-14)
      },
      {
        name: "Oporavljeni",
        values: recoveries.slice(-14)
      },
      {
        name: "Umrli",
        values: deaths.slice(-14)
      },
      {
        name: "Aktivni",
        values: activeCases.slice(-14)
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
    formatTooltipY: d => {
      console.log(d);
      return "abcd: " + d;
    }
  };

  const tooltipOptions2 = {
    formatTooltipY: d => `+${d}`
  };

  const barOptions = {
    spaceRatio: 0.05
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

<section style="display: flex; width: 100%;justify-content: space-between;">
  <div style="width: 50%;">
    <h4>Zadnjih 7 dana</h4>
    <Chart
      data={data3}
      {axisOptions}
      {lineOptions}
      {colors}
      height="440"
      type="line" />
  </div>
  <div style="width: 50%;">
    <h4>Zadnjih 14 dana</h4>
    <Chart
      data={data4}
      {axisOptions}
      {lineOptions}
      {colors}
      height="440"
      type="line" />
  </div>
</section>

<section>
  <h4>Novi slučajevi po danima</h4>
  <Chart
    data={data2}
    {axisOptions}
    {barOptions}
    tooltipOptions={tooltipOptions2}
    colors={['light-blue', 'red']}
    height="440"
    type="bar" />
</section>
