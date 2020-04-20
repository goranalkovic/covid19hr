<script>
  import Chart from "svelte-frappe-charts";
  export let dateAxis = [];
  export let input = [];

  let data = {
    labels: dateAxis,
    datasets: [
      {
        values: [...input]
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
    <span>
      R
      <sub>0</sub>
    </span>
  </h4>
  <Chart
    {data}
    {axisOptions}
    {lineOptions}
    {tooltipOptions}
    {colors}
    height="440"
    type="line" />
</section>
