<script>
    import * as d3 from 'd3';
    let width = 400;
    let height = 300;

    export let data = [];

    // Adding color/bar chart
    let margin = { top:70, right: 150, bottom: 30, left:60 };
    let innerWidth = width - margin.left - margin.right;
    let innerHeight = height - margin.top - margin.bottom;

    // Bar setup
    $: xScale = d3.scaleBand()
        .domain(data.map(d => d.label))
        .range([0, innerWidth])
        .padding(0.2);

    $: yScale = d3.scaleLinear()
        .domain([0, d3.max(data, d=> d.value) || 1])
        .range([innerHeight, 0]);

    $: colorScale = d3.scaleOrdinal(d3.schemeTableau10)
        .domain(data.map(d => d.label));

    $: data = data.sort((a,b) => a.year - b.year);

    // Adding axes
    let xAxis, yAxis;
    $: if (xAxis && yAxis) {
        d3.select(xAxis).call(d3.axisBottom(xScale));
        d3.select(yAxis).call(
            d3.axisLeft(yScale)
            .tickFormat(d => Number.isInteger(d) ? d : "")
            .tickValues(d3.range(0, d3.max(data, d => d.value) + 1))

        );
    }

    // Annotation
    $: maxBar = d3.greatest(data, d => d.value);

</script>
<div class="container">
    <svg viewBox="0 0 {width} {height}">
        <text
            x={margin.left + innerWidth / 2}
            y={margin.top / 2}
            text-anchor="middle"
            class="chart-title">
            Projects Per Year
        </text>
        <g transform="translate({margin.left}, {margin.top + innerHeight})"
        bind:this={xAxis} />
        <g transform="translate({margin.left}, {margin.top})"
        bind:this={yAxis} />
        <g transform="translate({margin.left}, {margin.top})">
            <!-- Draw bars-->
            {#each data as d}
            <rect
                x={xScale(d.label)}
                y={yScale(d.value)}
                width={xScale.bandwidth()}
                height={innerHeight - yScale(d.value)}
                fill={colorScale(d.label)}
                />
            {/each}

            {#if maxBar}
                <!-- highlight outline around the tallest bar -->
                <rect
                    x={xScale(maxBar.label)}
                    y={yScale(maxBar.value)}
                    width={xScale.bandwidth()}
                    height={innerHeight - yScale(maxBar.value)}
                    fill="none"
                    stroke="currentColor"
                    stroke-width="2"
                />
                <!-- leader line -->
                <line
                    x1={xScale(maxBar.label) + xScale.bandwidth()}
                    y1={yScale(maxBar.value) + (innerHeight - yScale(maxBar.value)) / 2}
                    x2={xScale(maxBar.label) + xScale.bandwidth() + 30}
                    y2={yScale(maxBar.value) + (innerHeight - yScale(maxBar.value)) / 2}
                    stroke="currentColor"
                    stroke-width="1"
                />
                <!-- annotation text at end of leader line -->
                <text
                    x={xScale(maxBar.label) + xScale.bandwidth() + 35}
                    y={yScale(maxBar.value) + (innerHeight - yScale(maxBar.value)) / 2}
                    dominant-baseline="middle"
                    class="annotation">
                    Year with most projects
                </text>
            {/if}


            <!-- x-axis label -->
            <text
                x={innerWidth / 2}
                y={innerHeight + margin.bottom + 10}
                text-anchor="middle"
                class="axis-label">
                Year
            </text>

            <!-- y-axis label -->
            <text
                x={-(innerHeight / 2)}
                y={-margin.left + 30}
                text-anchor="middle"
                transform="rotate(-90)"
                class="axis-label">
                Number of Projects
            </text>
        </g>

    </svg>
    <ul class="legend">
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
        padding-bottom: 5em;
    }

    svg {
        max-width: 100%;
        height: auto;
        overflow: visible;
    }
    .chart-title {
       font-size: 1em;
       font-weight: bold;
       fill: currentColor;
    }

    .legend {
        flex: 1;
    }

    .annotation {
        fill: currentColor;
        font-size: 0.7em;
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
