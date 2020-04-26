<script>
  import Chart from "svelte-frappe-charts";
  export let dateAxis = [];
  export let input = [];
  export let r0avg = 1.1;

  const round = num => {
    return +(Math.round(num + "e+3") + "e-3");
  };

  let data = {
    labels: dateAxis,
    datasets: [
      {
        values: [...input]
      }
    ],
    yMarkers: [
      {
        label: "Prosječna vrijednost",
        value: r0avg,
        options: { labelPos: "left" }
      },
      {
        label: "Još OK",
        value: 1
      },
      {
        label: "Nije optimalno",
        value: 2
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
    //   formatTooltipX: d => (d + "").toUpperCase(),
    formatTooltipY: d => "R<sub>0</sub> = " + d
  };

  const colors = ["purple"];
</script>

<style>

</style>

<section>
  <h4>
    <span>R₀</span>
  </h4>
  <Chart
    {data}
    {axisOptions}
    {lineOptions}
    {tooltipOptions}
    {colors}
    height="440"
    type="line" />

  <p style="text-align: center; margin: 1rem 0;">
    <strong>Prosječni R₀ :</strong>
    {round(r0avg)}
  </p>
</section>
