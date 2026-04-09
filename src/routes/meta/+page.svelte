<svelte:head>
  <title>Meta</title>
</svelte:head>

<h1>Meta</h1>
<script>
  import { base } from '$app/paths';
  import { browser } from '$app/environment';
  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  import BarHorizontal from '../../lib/BarHorizontal.svelte';
  import {
      computePosition,
      autoPlacement,
      offset,
  } from '@floating-ui/dom';
    import Bar from '../../lib/Bar.svelte';
    import LineChart from '../../lib/LineChart.svelte';

  let locData = [];
  let commits = [];

  onMount(async () => {
      locData = await d3.csv(`${base}/loc.csv`, row => ({
          ...row,
          line: Number(row.line),
          length: Number(row.length),
          depth: Number(row.depth),
          date: new Date(row.date + "T00:00" + row.timezone),
          datetime: new Date(row.datetime)
      }));

      commits = d3.groups(locData, d => d.commit).map(([commit, lines]) => {
        let first = lines[0];
        let {author, date, time, timezone, datetime} = first;
        let ret = {
          id: commit,
          url: "https://github.com/vis-society/lab-7/commit/" + commit,
          author, date, time, timezone, datetime,
          hourFrac: datetime.getHours() + datetime.getMinutes() / 60,
          totalLines: lines.length,
          lines: lines
        };
        return ret;
      });

      commits = d3.sort(commits, d => -d.totalLines);

  });

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
        .range([5, 30]);

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
            if (!clickedCommits.includes(commit)) { // Not already selected, add to selection
              clickedCommits = [...clickedCommits, commit];
            } else { // Deselect from selection
              clickedCommits = clickedCommits.filter(c => c!== commit);
            }
        }
    }

    // Making commits clickable
    let clickedCommits = [];

    // Match bar chart to selected dotted commit(s)

    $: selectedCounts = d3.rollup(
        selectedLines,
        v => v.length,
        d => d.type
    );

    $: allTypes = Array.from(new Set(locData.map(d=> d.type)));

    $: barData = allTypes.map(type => ({ label: String(type), value: selectedCounts.get(type)  || 0}))

    export function updateBarTitle(selectedCommits) {
        let title = '';
        if (selectedCommits && selectedCommits.length > 0) {
            title = `Lines of Code: ${selectedCommits.length} Selected commits`;
        } else {
            title = "Lines of Code: Breakdown";
        }
        return title;

    }

    $: title = updateBarTitle(selectedCommits);

    // Lab 8 Brushing
    let brushSelection;
    $: brushSelection = null;

    function brushed (evt) {
        brushSelection = evt.selection;
    }


    function isCommitBrushed (commit) {
	if (!brushSelection) {
		return false;
	}
    let min = {x: brushSelection[0][0], y: brushSelection[0][1]};
    let max = {x: brushSelection[1][0], y: brushSelection[1][1]};
    let x = xScale(commit.date);
    let y = yScale(commit.hourFrac);
    return x >= min.x && x <= max.x && y >= min.y && y <= max.y;

    }

    $: brushedCommits = brushSelection ? commits.filter(isCommitBrushed) : [];
    $: selectedCommits = Array.from(new Set([...clickedCommits, ...brushedCommits]));
    $: selectedLines = (selectedCommits.length > 0 ? selectedCommits : commits).flatMap(d => d.lines);

    let svg;

    $: if (browser && svg) {
        d3.select(svg).call(d3.brush()
        .extent([[usableArea.left, usableArea.top], [usableArea.right, usableArea.bottom]])
        .on("start brush end", brushed));
        d3.select(svg).selectAll(".dots, .overlay ~ *").raise();
    }

    // Data wrangling: lines edited by date
    let linesByDate = [];
    $: {
	// 1. Get the count for each date in the data
	const rolled = d3.rollups(
		locData,
		v => v.length,
		d => d3.timeDay.floor(d.datetime)
	).map(([date, count]) => ({ date, count }));

	// 2. Get an array of all days covered by the data
	const [minDate, maxDate] = d3.extent(rolled, d => d.date);
	const allDays = d3.timeDays(minDate, d3.timeDay.offset(maxDate, 1));

	// 3. Build linesByDate by filling all undefined dates with 0 counts
	linesByDate = allDays.map(date => ({
		date,
		count: rolled.find(d => d.date.getTime() === date.getTime())?.count ?? 0
	}));
}

</script>

<div>
    <svg
    bind:this={svg}
    viewBox="0 0 {width} {height}">
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
            class:selected={selectedCommits.includes(commit)}
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

<section class=bar-horizontal>
  <BarHorizontal data={barData} title={title}/>
</section>

<section class=line-chart>
    <LineChart data = {linesByDate} />
</section>

<style>
  section {
    padding-bottom: 0em;
  }

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

  @keyframes marching-ants {
    to {
        stroke-dashoffset: -8;
    }
  }

  svg :global(.selection) {
    fill-opacity: 10%;
    stroke: light-dark(black, white);
    stroke-opacity: 70%;
    stroke-dasharray: 5 3;
    animation: marching-ants 2s linear infinite;
  }

</style>
