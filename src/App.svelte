<script>
  import data from "$data/data.js";
  import Line from "$components/Line.svelte";
  import Tooltip from "$components/Tooltip.svelte";
  import HoverEvents from "$components/HoverEvents.svelte";
  import * as d3 from 'd3';
  
  const margin = { top: 15, right: 20, bottom: 40, left: 50 };
  let width = 600;
  let height = 400;
  $: innerWidth = width - margin.left - margin.right;
  let innerHeight = height - margin.top - margin.bottom;

  const parseDate = d3.timeParse("%Y");

  data.forEach(d => {
    d.Fecha = parseDate(d.Fecha);
  });

  $: xScale = d3.scaleUtc()
    .domain(d3.extent(data, (d) => (d.Fecha)))
    .range([0, innerWidth]);

  const yScale = d3.scaleLinear()
    .domain([100, d3.max(data, d => d.Indice)])
    .range([innerHeight, 0]);

  $: lineAsequibilidad = d3.line()
  .curve(d3.curveBasis)
    .x((d) => xScale(d.Fecha))
    .y((d) => yScale(d.Indice));

  const formatYear = d3.timeFormat("%Y");

  let maxDate = data[data.length - 1].Fecha;
    
  let hoveredDate = maxDate;

</script>

<div class="main" bind:clientWidth={width}>
<h1>Salario real (1990=100)</h1>
<div class="chart-container">
  <svg {width} {height}>
    <g class="inner-chart" transform="translate({margin.left}, {margin.top})">
      {#each [100,105,110,115] as tick, i}
       {#if i !== 0}
          <g class="tick" transform="translate(0, {yScale(tick)})">
              <text text-anchor="start" dominant-baseline="bottom" x="-40" y="-10" dy="0.35em">{tick}</text>
              <line x1="-40" x2="{innerWidth}" y1="0" y2="0" stroke="hsla(215, 15%, 91%, 1)"></line>
          </g>
        {/if}
      {/each}
        {#each xScale.ticks(10) as tick, i}
            <g class="tick" transform="translate({xScale(tick)}, 0)">
                <text text-anchor="middle" dominant-baseline="middle" x="0" y={innerHeight + 15}>{formatYear(tick)}</text>
                <line x1="0" x2="0" y1="{innerHeight}" y2="{innerHeight + 5}" stroke="black"></line>
            </g>
        {/each}
        <Line {xScale} {yScale} {data} {hoveredDate} {innerHeight}/>  
        <HoverEvents bind:hoveredDate {maxDate} {margin} {innerWidth} {innerHeight} {xScale}/>
        <Tooltip {hoveredDate} {xScale} {yScale} {innerWidth} {data}/>
    </g>
  </svg>
</div>
<p class="fuente">Fuente: OCDE</p>
</div>
<style>

.fuente {
  font-size: 14px;
  font-style: italic;
}

h1 {
    font-size: 16px;
    margin-bottom: 8px;
    font-weight: 600;
    text-align: left;

  }

/* Estilos para dispositivos con pantallas más grandes (ordenadores) */
@media (min-width: 768px) {
  .chart-container {
    max-width: 600px;
  }
}

/* Estilos para dispositivos con pantallas más pequeñas (móviles) */
@media (max-width: 767px) {
  .chart-container {
    max-width: 100%;
  }
}
  .tick text {
    font-size: 12px;
    fill: #666;
  }
  line {
    stroke-width: 1px;
  }

</style>