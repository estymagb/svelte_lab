<script>
    import * as d3 from "d3";

    export let commits = [];

    let width = 1000, height = 600;

    // yScale: daytimes, xScale: days
    $: [minDate, maxDate] = d3.extent(commits.map(d => d.date));
    $: maxDatePlusOne  = new Date(maxDate);
    $: maxDatePlusOne.setDate(maxDatePlusOne.getDate() + 1);

    let margin = { top: 20, right: 20, bottom: 30, left: 60 };
    let usableArea = {
        top: margin.top,
        right: width - margin.right,
        bottom: height - margin.bottom,
        left: margin.left
    };

    usableArea.width = usableArea.right - usableArea.left;
    usableArea.height = usableArea.bottom - usableArea.top;

    $: xScale = d3.scaleTime()
        .domain([minDate, maxDatePlusOne])
        .range([usableArea.left, usableArea.right])
        .nice();

    $: yScale = d3.scaleLinear()
        .domain([24, 0])
        .range([usableArea.bottom, usableArea.top])

    let xAxis, yAxis;
    $: {
        d3.select(xAxis).call(d3.axisBottom(xScale));
        d3.select(yAxis).call(d3.axisLeft(yScale).tickFormat(d => String(d% 24).padStart(2, "0") + ":00"));
    }

    let yAxisGridlines;
    $: {
        d3.select(yAxisGridlines).call(
            d3.axisLeft(yScale)
            .tickFormat("")
            .tickSize(-usableArea.width)
        );
    }


</script>
<svg viewBox= "0 0 {width} {height}">
    <text
            x={usableArea.width - (usableArea.right /2) + 100}
            y={margin.top / 2 - 20}
            text-anchor="middle"
            class="chart-title">
            Commits by time of day
    </text>
    <g transform="translate(0, {usableArea.bottom})" bind:this={xAxis} />
    <g class="gridlines" transform="translate({usableArea.left}, 0)" bind:this={yAxisGridlines} />
    <g transform="translate({usableArea.left}, 0)" bind:this={yAxis} />
    <g class="dots">
    {#each commits as commit, index}
    <circle
        cx={ xScale(commit.datetime)}
        cy={yScale(commit.hourFrac)}
        r="5"
        fill="steelblue"
    />
    {/each}
    </g>
</svg>

<style>
    svg {
        overflow: visible;
        padding: 1.5em;
    }

    .chart-title {
       font-size: 2em;
       font-weight: bold;
       fill: currentColor;
       padding-bottom: 5em;
    }

    .gridlines {
        stroke-opacity: .2;
    }
</style>
