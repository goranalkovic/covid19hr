<script>
  import Chart from "svelte-frappe-charts";

  export let input;

  let data = {
    labels: ["Aktivni", "Oporavljeni", "Umrli"],
    datasets: [
      {
        values: input
      }
    ]
  };

  const colors = ["light-blue", "light-green", "red"];

  const total = input => input.reduce((a, b) => a + b, 0);
</script>

<style>
  h5 {
    margin-top: 2em;
  }
</style>

<section>
  <h5>
    Ukupno
    <strong>{total(input)}</strong>
    slučaj
  </h5>

  <Chart {data} {colors} height="160" type="percentage" />
</section>
