<script>
    import * as d3 from 'd3';
    export let data = [];

    let width = 1000, height = 300;
    let margin = { top: 20, right: 20, bottom: 60, left: 60 };
    let usableArea = {
        top: margin.top,
        right: width - margin.right,
        bottom: height - margin.bottom,
        left: margin.left
    };

    $: xScale = d3.scaleTime()
        .domain(d3.extent(data, d => d.date))
        .range([usableArea.left, usableArea.right])
        .nice();

    $: yScale = d3.scaleLinear()
        .domain([0, d3.max(data, d => d.count)])
        .range([usableArea.bottom, usableArea.top])
        .nice();

    $: line = d3.line()
        .x(d => xScale(d.date))
        .y(d => yScale(d.count))
        .curve(d3.curveBumpX);

    let xAxis, yAxis;
    $: if (xAxis && yAxis) {
        d3.select(xAxis).call(d3.axisBottom(xScale));
        d3.select(yAxis).call(d3.axisLeft(yScale));
    }
</script>

<h3 class="line-title">Lines Edited by Day</h3>
<svg viewBox= "0 0 {width} {height}">
    <g transform="translate({margin.left-20}, {height - margin.bottom + 2})"
    bind:this={xAxis} />
    <g transform="translate({1.6*usableArea.left}, {margin.top - 20})"
    bind:this={yAxis} />
    <path
        d={line(data)}
        fill="none"
        stroke="steelblue"
        stroke-width="2"
    />

    {#each data as d}
        <circle
            cx={xScale(d.date)}
            cy={yScale(d.count)}
            r="3"
            fill="steelblue"
        />
    {/each}

     <!-- x-axis label -->
    <text
        x={usableArea.left + (usableArea.right - usableArea.left) / 2}
        y={height - 5}
        text-anchor="middle"
        class="axis-label">
        Date
    </text>

    <!-- y-axis label -->
    <text
        x={-(usableArea.top + (usableArea.bottom - usableArea.top) / 2)}
        y={10}
        text-anchor="middle"
        transform="rotate(-90)"
        class="axis-label">
        Number of Lines Edited
    </text>

</svg>

<style>
    svg {
        overflow: visible;
        padding-bottom: 5em;
    }
    .line-title {
        text-align: center;
        font-size: 3.0em;
    }

    .axis-label {
        font-size: 2em;
        padding: 3em;
        fill: currentColor;
    }
</style>
