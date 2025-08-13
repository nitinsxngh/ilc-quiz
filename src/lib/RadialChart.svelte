<script>
  import { onMount } from "svelte";
  import Chart from "chart.js/auto";
  import { store, radialChartConfig } from "../assets/store.js";

  let chart = null;
  let myChart = null;
  let minDataValue = 0.05;

  store.subscribe((value) => {
    if (myChart) updateData();
  });

  function updateData() {
    let values = [];

    for (let [key, value] of Object.entries(store.weightedPoints)) {
      values.push(value + minDataValue);
    }

    myChart.data.datasets[0].data = values;

    myChart.update();
  }

  function createData() {
    let labels = [];
    let values = [];

    for (let [key, value] of Object.entries($store.points)) {
      labels.push(key);
      values.push(((value / $store.maxPoints[key]) + minDataValue) / (1 + minDataValue));
    }

    let data = {
      labels: labels,
      datasets: [
        {
          label: $store.strings['radarChartLabel'],
          lineTension: 0.1,
          data: values,
          backgroundColor: [
            "rgba(255, 99, 132, 0.5)",
          ],
          borderColor: [
            "rgba(255, 99, 132, 1)",
          ],
          borderWidth: 1,
        },
      ],
    };

    return data;
  }

  function createChart() {
    const ctx = chart.getContext("2d");
    let options = JSON.parse(JSON.stringify(radialChartConfig)); // Deep copy to avoid modifying original
    
    // Responsive font sizing based on screen width
    const isMobile = window.innerWidth < 768;
    const isTablet = window.innerWidth < 1024;
    
    if (isMobile) {
      options.scales.r.pointLabels.font.size = 10;
      options.plugins.title.font.size = 14;
    } else if (isTablet) {
      options.scales.r.pointLabels.font.size = 11;
      options.plugins.title.font.size = 15;
    } else {
      options.scales.r.pointLabels.font.size = 12;
      options.plugins.title.font.size = 16;
    }
    
    options.plugins.title.text = $store.strings['radarChartLabel'];

    myChart = new Chart(ctx, {
      type: "radar",
      data: createData(),
      options: options,
    });
  }

  onMount(() => {
    createChart();
    
    // Handle window resize for responsive chart
    const handleResize = () => {
      if (myChart) {
        myChart.destroy();
        createChart();
      }
    };
    
    window.addEventListener('resize', handleResize);
    
    return () => {
      window.removeEventListener('resize', handleResize);
    };
  });

  let classes = $$props.class;
</script>

<div
  class="h-fit min-w-screen rounded-xl p-2 transition-all text-box {classes}">
  <div class="relative w-full h-64 md:h-80 lg:h-96">
    <canvas bind:this="{chart}"></canvas>
  </div>
</div>
