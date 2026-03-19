<script>
    import * as d3 from 'd3';
    let width = 400;
    let height = 300;

    export let data = [];

    // Adding color/bar chart
    let margin = { top:70, right: 50, bottom: 30, left:70 };
    let innerWidth = width - margin.left - margin.right;
    let innerHeight = height - margin.top - margin.bottom;

    // Bar setup
    $: xScale = d3.scaleLinear()
        .domain([0, d3.max(data, d=> d.value) || 1])
        .range([0, innerWidth]);

    $: yScale = d3.scaleBand()
        .domain(data.map(d => d.label))
        .range([0, innerHeight])
        .padding(0.2);


    $: colorScale = d3.scaleOrdinal(d3.schemeSet3)
        .domain(data.map(d => d.label));

    // Adding axes
    let xAxis, yAxis;
    $: if (xAxis && yAxis) {
        d3.select(xAxis).call(d3.axisBottom(xScale));
        d3.select(yAxis).call(d3.axisLeft(yScale));
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
            Lines of Code by Language
        </text>
        <g transform="translate({margin.left}, {margin.top + innerHeight})"
        bind:this={xAxis} />
        <g transform="translate({margin.left-1}, {margin.top})"
        bind:this={yAxis} />
        <g transform="translate({margin.left}, {margin.top})">
            <!-- Draw bars-->
            {#each data as d}
            <rect
                y={yScale(d.label)}
                width={xScale(d.value)}
                height={yScale.bandwidth()}
                fill={colorScale(d.label)}
                />
            {/each}

            {#if maxBar}
                <!-- highlight outline around the tallest bar -->
                <rect
                    x={0}
                    y={yScale(maxBar.label)}
                    width={xScale(maxBar.value)}
                    height={yScale.bandwidth()}
                    fill="none"
                    stroke="currentColor"
                    stroke-width="2"
                />
                <!-- leader line -->
                <line
                    x1={xScale(maxBar.value)/2 }
                    y1={yScale(maxBar.label) + yScale.bandwidth()}
                    x2={xScale(maxBar.value)/2 +5}
                    y2={yScale(maxBar.label) + yScale.bandwidth() + 30}
                    stroke="currentColor"
                    stroke-width="1"
                />
                <!-- annotation text at end of leader line -->
                <text
                    x={xScale(maxBar.value)/2 +5}
                    y={yScale(maxBar.label) + yScale.bandwidth() + 35}
                    dominant-baseline="middle"
                    class="annotation">
                    Most lines of code
                </text>
            {/if}


            <!-- x-axis label -->
            <text
                x={innerHeight/2 + margin.left - 30}
                y={innerWidth - 30}
                text-anchor="middle"
                class="axis-label">
                Lines of Code
            </text>

            <!-- y-axis label -->
            <text
                x={-(innerHeight / 2)}
                y={-margin.left + 30}
                text-anchor="middle"
                transform="rotate(-90)"
                class="axis-label">
                Programming Language
            </text>
        </g>

    </svg>
    <!-- Legend -->
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
