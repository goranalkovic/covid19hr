<script>
  import Chart from "svelte-frappe-charts";
  export let dateAxis;
  export let totalCases;
  export let recoveries;
  export let activeCases;
  export let deaths;
  //

  const height = 200;
  const offset = Math.log(10);

  //
  const scale = (num, in_min, in_max, out_min, out_max) => {
    return ((num - in_min) * (out_max - out_min)) / (in_max - in_min) + out_min;
  };
  const round = (num, dec = 3) => {
    return num.toFixed(dec);
  };

  //

  const toLogScale = (input) => {
    let output = [];

    for (let val of input) {
      // let calculated = Math.log10(val);
      let calculated =
        (height * (Math.log(val) - offset)) / (Math.log(70) - offset);

      if (isFinite(calculated) && !isNaN(calculated) && calculated != null)
        output.push(scale(calculated, -250, 600, 1, 2500));
      else output.push(1);
    }
    return [...output];
  };
  //
  const generateLogData = (arr, name) => {
    return {
      labels: dateAxis,
      datasets: [
        {
          name: name,
          values: toLogScale(arr),
        },
      ],
    };
  };

  const genLogTooltip = (arr) => {
    return {
      formatTooltipY: (d) => {
        let logged = toLogScale(arr);
        let index = logged.indexOf(d);
        let num = arr[index] != null ? arr[index] : 0;
        return round(num, 0);
      },
    };
  };

  //
  const axisOptions = {
    xIsSeries: true,
  };

  const lineOptions = {
    regionFill: true,
    hideDots: true,
  };
</script>

<style>
  section {
    width: 90vw;
    margin: 0 auto;
    max-width: 1400px;
  }
</style>

<section style="display: flex; flex-direction: column; align-items: center;">
  <h1>Logaritamski</h1>
  <svg style="width: 400px;height: 40px; margin-top: 20px;">
    <g class="chart-legend">
      <g transform="translate(0, 0)">
        <rect
          class="legend-bar"
          x="0"
          y="0"
          width="100"
          height="2px"
          fill="#b8c2cc" />
        <text
          class="legend-dataset-text"
          x="0"
          y="0"
          dy="20px"
          font-size="12px"
          text-anchor="start"
          fill="#555b51">
          Ukupno
        </text>
      </g>
      <g transform="translate(100, 0)">
        <rect
          class="legend-bar"
          x="0"
          y="0"
          width="100"
          height="2px"
          fill="#98d85b" />
        <text
          class="legend-dataset-text"
          x="0"
          y="0"
          dy="20px"
          font-size="12px"
          text-anchor="start"
          fill="#555b51">
          Oporavljeni
        </text>
      </g>
      <g transform="translate(200, 0)">
        <rect
          class="legend-bar"
          x="0"
          y="0"
          width="100"
          height="2px"
          fill="#ff5858" />
        <text
          class="legend-dataset-text"
          x="0"
          y="0"
          dy="20px"
          font-size="12px"
          text-anchor="start"
          fill="#555b51">
          Umrli
        </text>
      </g>
      <g transform="translate(300, 0)">
        <rect
          class="legend-bar"
          x="0"
          y="0"
          width="100"
          height="2px"
          fill="#7cd6fd" />
        <text
          class="legend-dataset-text"
          x="0"
          y="0"
          dy="20px"
          font-size="12px"
          text-anchor="start"
          fill="#555b51">
          Aktivni
        </text>
      </g>
    </g>
  </svg>
  <div style="display: flex; width: 100%;justify-content: center;">
    <div
      style="width: 800px; max-width: 90vw"
      class="hide-legend hide-axis hide-x hide-y">
      <Chart
        data={generateLogData(totalCases, 'Ukupno')}
        {axisOptions}
        {lineOptions}
        tooltipOptions={genLogTooltip(totalCases)}
        colors={['dark-grey']}
        height="440"
        type="line" />
    </div>

  </div>
  <div
    style="display: flex; width: 100%;justify-content: center; flex-wrap: wrap">
    <div style="width: 440px;" class="hide-legend hide-axis hide-x hide-y">
      <Chart
        data={generateLogData(recoveries, 'Oporavljeni')}
        {axisOptions}
        {lineOptions}
        tooltipOptions={genLogTooltip(recoveries)}
        colors={['light-green']}
        height="440"
        type="line" />
    </div>
    <div style="width: 440px;" class="hide-x hide-y-line">
      <Chart
        data={generateLogData(deaths, 'Umrli')}
        {axisOptions}
        {lineOptions}
        tooltipOptions={genLogTooltip(deaths)}
        colors={['red']}
        height="440"
        type="line" />
    </div>
    <div style="width: 440px;" class="hide-x hide-y-line">
      <Chart
        data={generateLogData(activeCases, 'Aktivni')}
        {axisOptions}
        {lineOptions}
        tooltipOptions={genLogTooltip(activeCases)}
        colors={['light-blue']}
        height="440"
        type="line" />
    </div>
  </div>
</section>
