<script>
  import {onMount} from 'svelte';
  import Chart from 'chart.js';

  export let datasets;

  const lineDatasets = {};
  let xyDatasets = [];
  const barDatasets = {};

  let xyCanvas;
  let lineCanvas;
  let barCanvas;

  let xyChart;
  let lineChart;

  function processDatasets() {
    for (const [datasetName, dataset] of Object.entries(datasets)) {
      insertDataset(datasetName, dataset);
    }
  }

  function insertDataset(name, dataset) {
    const values = dataset.values;
    if (values.length === 0) {
      return;
    }
    const graphType = dataset.type;
    switch (graphType) {
      case 'xy':
        insertXYChart(name, dataset);
        break;
      case 'bar':
        insertBarChart(name, dataset);
        break;
      case 'line':
        insertLineChart(name, dataset);
        break;
      default:
    }
  }

  function insertBarChart(name, dataset) {
    const values = dataset.values;
    const xLabels = [];
    const data = [];
    for (const value of values) {
      xLabels.push(value[0]);
      data.push(value[1]);
    }
    barDatasets['labels'] = xLabels;
    barDatasets['datasets'] = [];
    barDatasets['datasets'].push({label: name, data: data, tension: 0.1});
  }

  function insertLineChart(name, dataset) {
    const values = dataset.values;
    const xLabels = [];
    const data = [];
    for (const value of values) {
      xLabels.push(value[0]);
      data.push(value[1]);
    }
    lineDatasets['labels'] = xLabels;
    lineDatasets['datasets'] = [];
    lineDatasets['datasets'].push({label: name, data: data});
  }

  function insertXYChart(name, dataset) {
    const points = dataset.values;
    const chartPoints = [];
    for (const point of points) {
      chartPoints.push({x: point[0], y: point[1]});
    }
    xyDatasets.push({label: name, data: chartPoints});
    xyDatasets = xyDatasets;
  }

  async function createChart() {
    processDatasets(datasets);
    if (xyDatasets.length) {
      xyChart = new Chart(xyCanvas, {
        type: 'scatter',
        data: {
          datasets: xyDatasets,
        },
      });
    } else {
      xyCanvas.hidden = true;
    }
    if (Object.keys(lineDatasets).length) {
      lineChart = new Chart(lineCanvas, {
        type: 'line',
        data: lineDatasets,
      });
    } else {
      lineCanvas.hidden = true;
    }
    if (Object.keys(barDatasets).length) {
      lineChart = new Chart(barCanvas, {
        type: 'bar',
        data: barDatasets,
      });
    } else {
      barCanvas.hidden = true;
    }
  }

  onMount(createChart);
</script>


<style>
  .graph {
    max-width: 300px;
  }
</style>


<div class="graph-container">
  <canvas class="graph" bind:this={xyCanvas} />
  <canvas class="graph" bind:this={barCanvas} />
  <canvas class="graph" bind:this={lineCanvas} />
</div>
