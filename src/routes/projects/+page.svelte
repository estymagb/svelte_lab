<script>
    import projects from "$lib/projects.json";
    import ProjectNarrative from "../../lib/ProjectNarrative.svelte";
    import Project from "$lib/Project.svelte";
    import Bar from '$lib/Bar.svelte';
    import {onMount} from 'svelte';
    import * as d3 from 'd3';

    let years = projects.map(proj => proj.year);
    let range = Math.max(...years) - Math.min(...years);

    let rawData = [];
    let wrangled = [];
    onMount(async() => {
        rawData = await d3.json('/lab6_example.json');
        wrangled = d3.rollups(
            rawData,
            v => d3.sum(v, d=> d.lines),
            d => d.language
        );
    });

</script>
<svelte:head>
  <title> Projects</title>
</svelte:head>

<h1>{projects.length} Projects over {range} Years</h1>

<section>
    <h2>Data wrangling result</h2>
    <pre>{JSON.stringify(wrangled, null, 2)}</pre>
    <Bar/>
</section>

<p>Scroll down to see my a timeline of my projects and how I've learned from them.</p>

<ProjectNarrative />

<p class="outro">Thanks for scrolling through my project story! Feel free to explore all of the projects at your leisure below.</p>

<section class= "projects" id="projects">
    {#each projects as p}
        <Project data={p}/>
    {/each}
</section>

<style>
    .outro {
        margin-bottom: 3em;
    }
</style>
