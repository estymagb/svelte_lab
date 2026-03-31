<script>
    import * as d3 from "d3";
    import {
        computePosition,
        autoPlacement,
        offset,
    } from '@floating-ui/dom';
    import Bar from "./Bar.svelte";

    export let commits = [];
    export let locBarData = [];

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
        .range([usableArea.bottom, usableArea.top]);


    // Creates radius scale for dots in 'Number of commits by datetime'
    $: rScale = d3.scaleSqrt()
        .domain(d3.extent(commits.map(c => c.totalLines)))
        .range([30, 5]);

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

    // Watches index of commits (loc.csv) whne mouse hovers
    let hoveredIndex = -1;
    $: hoveredCommit = commits[hoveredIndex] ?? hoveredCommit ?? {};

    // Cursor watcher to tie tooltip to mouse hover
    let cursor = {x: 0, y:0}

    // Prevent incorrect positioning based on document (webpage) size
    let commitTooltip;
    let tooltipPosition = {x:0, y:0}; // holds tooltipPosition to update in mouseenter evt

    async function dotInteraction (index, evt) {
        let hoveredDot = evt.target;
        if (evt.type === "mouseenter") {
            hoveredIndex = index;
            cursor = {x: evt.x, y: evt.y};
            tooltipPosition = await computePosition(hoveredDot, commitTooltip, {
                strategy: "fixed",
                middleware: [
                    offset(5),
                    autoPlacement()
                ],
            });
        }
        else if (evt.type === "mouseleave") {
            hoveredIndex = -1;
        } else if (evt.type === "click") {
            let commit = commits[index]
            if (!clickedCommits.includes(commit)) {
                clickedCommits = [...clickedCommits, commit];
            }
        } else {
            clickedCommits = clickedCommits.filter(c => c !== commit);
        }
    }

    // Making commits clickable
    let clickedCommits = [];

    // Match bar chart to selected dotted commit(s)
    $: selectedLines = (clickedCommits.length > 0 ? clickedCommits.flatMap(d => d.lines) : locBarData);

    $: selectedCounts = d3.rollup(
        selectedLines,
        v => v.length,
        d => d.type
    );

    $: allTypes = Array.from(new Set(locBarData.map(d=> d.type)));

    $: barData = allTypes.map(type => ({ label: String(type), value: selectedCounts.get(type)  || 0}))
    console.log(barData)

    export function updateBarTitle() {
        let title = '';
        if (clickedCommits.length > 0) {
            title = "Selected commits";
        } else {
            title = "Breakdown";
        }
        return title;
    }

</script>

<div>
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
            class:selected={clickedCommits.includes(commit)}
            on:click={ evt => dotInteraction(index, evt)}
            on:mouseenter={evt => dotInteraction(index, evt)}
            on:mouseleave={evt => dotInteraction(index, evt)}
            cx={ xScale(commit.datetime)}
            cy={yScale(commit.hourFrac)}
            r={rScale(commit.totalLines)}
            fill="steelblue"
            fill-opacity=0.4
        />
        {/each}
        </g>
    </svg>
    <dl class="info tooltip"
    bind:this={commitTooltip}
    hidden={hoveredIndex === -1} style="top:{tooltipPosition.y}px; left:{tooltipPosition.x}px">
        <dt>Commit</dt>
        <dd><a href="{hoveredCommit.url}" target="_blank">
            {hoveredCommit.id}
        </a></dd>
        <dt>Author</dt>
        <dd>{hoveredCommit.author}</dd>
        <dt>Date</dt>
        <dd>{hoveredCommit.datetime?.toLocaleString("en", {dateStyle: "full"})}</dd>
        <dt>Time</dt>
        <dd>{hoveredCommit.datetime?.toLocaleString("en", {timeStyle: "short"})}</dd>
        <dt>Lines</dt>
        <dd>{hoveredCommit.totalLines}</dd>
    </dl>
</div>

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

    circle {
        transition: 200ms;
        &:hover {
            fill:darkgreen;
        }
    }

    dl.info {
        display: grid;
        grid-template-columns: auto auto;

        transition-duration: 500ms;
        transition-property: opacity, visibility;

        &[hidden]:not(:hover, :focus-within) {
            opacity: 0;
            visibility: hidden;
        }
    }

    .tooltip {
        position: fixed;
        top: 1em;
        left: 1em;
        box-shadow: 1em;
        background-color: light-dark(oklch(100% 0% 0/ 80%), rgb(46, 46, 46));
        border-radius: 10px;
        backdrop-filter: blur(1em);
        padding: 1em;
    }

    .selected {
        fill: var(--color-accent)
    }


</style>
