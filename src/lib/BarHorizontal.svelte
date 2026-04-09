<script>
    import * as d3 from 'd3';
    let width = 400;
    let height = 300;

    export let data = [];
    export let title = "";

    // Adding color/bar chart
    let margin = { top:40, right: 50, bottom: 70, left:70 };
    let innerWidth = width - margin.left - margin.right;
    let innerHeight = 0.5*(height - margin.top - margin.bottom);

    // Fix spacing
    let barRecHeight = 10;

    // Bar setup
    $: xScale = d3.scaleLinear()
        .domain([0, d3.max(data, d=> d.value) || 1])
        .range([0, innerWidth]);

    $: yScale = d3.scaleBand()
        .domain(data.map(d => d.label))
        .range([0, innerHeight])
        .padding(0.1);

    $: colorScale = d3.scaleOrdinal(d3.schemeSet3)
        .domain(data.map(d => d.label));

    // Adding axes
    let xAxis, yAxis;
    $: if (xAxis && yAxis) {
        d3.select(xAxis).call(
            d3.axisBottom(xScale)
            .ticks(Math.min(d3.max(data, d=> d.value), 10))
        );
        d3.select(yAxis).call(d3.axisLeft(yScale));
    }

    // Annotation
    $: maxBar = d3.greatest(data, d => d.value);


</script>
<div class="container">
    <svg viewBox="0 0 {width} {0.6*height}">
        <text
            x={margin.left + innerWidth / 2}
            y={margin.top / 2}
            text-anchor="middle"
            class="chart-title">
            {title}
        </text>
        <g transform="translate({margin.left}, {margin.top + innerHeight})"
        bind:this={xAxis} />
        <g transform="translate({margin.left}, {margin.top})"
        bind:this={yAxis} />
        <g transform="translate({margin.left +1}, {margin.top})">
            <!-- Draw bars-->
            {#each data as d}
            <rect
                y={yScale(d.label)}
                width={xScale(d.value)}
                height={barRecHeight}
                fill={colorScale(d.label)}
                />
            {/each}

            {#if maxBar}
                <!-- highlight outline around the tallest bar -->
                <rect
                    x={0}
                    y={yScale(maxBar.label)}
                    width={xScale(maxBar.value)}
                    height={barRecHeight}
                    fill="none"
                    stroke="currentColor"
                    stroke-width="2"
                />
                <!-- annotation text -->
                <text
                    x={xScale(maxBar.value) + 5}
                    y={yScale(maxBar.label) + 5}
                    dominant-baseline="middle"
                    text-anchor="start"
                    class="annotation">
                    Most lines of code
                </text>
            {/if}


            <!-- x-axis label -->
            <text
                x={innerWidth/2}
                y={1.4*innerHeight}
                text-anchor="middle"
                class="axis-label">
                Lines of Code
            </text>

            <!-- y-axis label -->
            <text
                y={-margin.left}
                text-anchor="middle"
                transform="rotate(-90)"
                class="axis-label">
                <tspan x="-40" dy="0.5em">Programming</tspan>
                <tspan x="-40" dy="1.0em">Language</tspan>
            </text>
        </g>

    </svg>
    <!-- Legend -->
    <ul class="legend" >
        {#each data as d}
            <li style="--color: {colorScale(d.label)}">
                <span class="swatch"></span>
                {d.label}<em>({d.value})</em>
            </li>
        {/each}
    </ul>
</div>

<style>
    .container {
        display: flex;
    }

    svg {
        overflow: visible;
    }
    .chart-title {
       font-size: 1em;
       font-weight: bold;
       fill: currentColor;
    }

    .legend {
        flex: 1;
        padding-top: 10%;
    }

    .annotation {
        fill: currentColor;
        font-size: 0.4em;
        font-style: italic;
    }

    .axis-label {
        font-size: 0.8em;
        fill: currentColor;
    }
    .swatch {
        width: 20px;
        height: 20px;
        background-color: var(--color);

    }

    li {
        display: flex;
        align-items: center;
        gap: 10px;

    }
</style>
