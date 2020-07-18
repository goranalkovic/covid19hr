<script>
  import Chart from "svelte-frappe-charts";
  export let dateAxis;
  export let totalCases;
  export let totalCasesDelta;
  export let deathsDelta;
  export let recoveries;
  export let activeCases;
  export let activeCasesDelta;
  export let deaths;

  //

  let tempDateAxis = [];

  for (let d of dateAxis) {
    tempDateAxis.push(
      new Intl.DateTimeFormat("hr", {
        month: "narrow",
        day: "numeric",
      }).format(d)
    );
  }

  dateAxis = [...tempDateAxis];

  //

  const round = (num, dec = 3) => {
    return num.toFixed(dec);
  };

  //

  const data = {
    labels: dateAxis,
    datasets: [
      {
        name: "Ukupno",
        values: totalCases,
      },
      {
        name: "Oporavljeni",
        values: recoveries,
      },
      {
        name: "Umrli",
        values: deaths,
      },
      {
        name: "Aktivni",
        values: activeCases,
      },
    ],
  };

  const data2 = {
    labels: dateAxis,
    datasets: [
      { name: "Novi slučajevi", values: totalCasesDelta },
      { name: "Nove smrti", values: deathsDelta },
    ],
  };

  const data3 = {
    labels: dateAxis,
    datasets: [
      { name: "Novi slučajevi", values: totalCasesDelta },
      { name: "Aktivni slučajevi", values: activeCasesDelta },
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
    formatTooltipY: (d) => {
      return "abcd: " + d;
    },
  };

  const barOptions = {
    spaceRatio: 0.05,
  };

  const barOptions2 = {
    spaceRatio: 0.5,
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
    "light-grey",
  ];
</script>

<style>
  section {
    width: 90vw;
    margin: 0 auto;
    max-width: 1400px;
  }
</style>

<section>
  <h1>Pregled po danima</h1>
  <Chart {data} {axisOptions} {lineOptions} {colors} height="440" type="line" />
</section>

<section>
  <h1>Novi slučajevi po danima</h1>
  <Chart
    data={data2}
    {axisOptions}
    {barOptions}
    colors={['light-blue', 'red']}
    height="440"
    type="bar" />
</section>

<section>
  <h1>Novi aktivni slučajevi po danima</h1>
  <Chart
    data={data3}
    {axisOptions}
    {barOptions}
    colors={['light-blue', 'light-green']}
    height="440"
    type="bar" />
</section>
