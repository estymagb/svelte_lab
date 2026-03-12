<script>
    import projects from "$lib/projects.json";
    import ProjectNarrative from "../../lib/ProjectNarrative.svelte";
    import Project from "$lib/Project.svelte";

    let years = projects.map(proj => proj.year);
    let range = Math.max(...years) - Math.min(...years);
</script>
<svelte:head>
  <title> Projects</title>
</svelte:head>

<h1>{projects.length} Projects over {range} Years</h1>

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
