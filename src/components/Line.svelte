<script>
    export let data;
    export let xScale;
    export let yScale;
    export let hoveredDate;
    export let innerHeight;
    import * as d3 from 'd3';

    $: dataBeforeHover = data.filter((d) => d.Fecha <= hoveredDate);

    import { line } from "d3-shape";

    $: lineGenerator = line()
        .curve(d3.curveBasis)
        .x((d) => xScale(d.Fecha))
        .y((d) => yScale(d.Indice));


    $: areaAsequibilidad = d3.area()
        .curve(d3.curveBasis)
        .x(d => xScale(d.Fecha))
        .y0(innerHeight)
        .y1(d => yScale(d.Indice));

</script>

<path
    d={lineGenerator(dataBeforeHover)}
    stroke="steelblue"
    fill="transparent"
    stroke-width="2"
/>

<path
    d={lineGenerator(data)}
    stroke="steelblue"
    fill="transparent"
    stroke-width="2"
    opacity=".35"
/>

<path
    d={areaAsequibilidad(dataBeforeHover)}
    fill="steelblue"
    fill-opacity="0.3"
/>

<path
    d={areaAsequibilidad(data)}
    fill="steelblue"
    fill-opacity="0.1"
/>