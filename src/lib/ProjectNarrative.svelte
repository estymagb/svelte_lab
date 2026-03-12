<script>
    import Scrolly from "svelte-scrolly";
    import projects from "$lib/projects.json";
    let scrollyProgress = 0;
    let sorted_projects = projects.sort((a, b) => a.year - b.year);
    let progressPerProject = 100 / sorted_projects.length;
    $: activeProjectIdx = Math.min(sorted_projects.length-1, Math.floor(scrollyProgress / progressPerProject));

</script>

<div class="scrolly-wrapper">
    <Scrolly bind:progress={scrollyProgress} >
        <!-- Story here-->
        <section class="step">
            {#each sorted_projects as sp}
                <div class="step-content">
                        <h3>{sp.title}</h3>
                        <p>{sp.story}</p>
                </div>
            {/each}
        </section>

        <svelte:fragment slot="viz">
            <!-- Visualizations *that wll stay sticky-->
            <div class=project-detail>
                <h3>{sorted_projects[activeProjectIdx].year}</h3>
                <img src={sorted_projects[activeProjectIdx].image} alt={sorted_projects[activeProjectIdx].description}/>
                <progress value={scrollyProgress} max="100">{Math.round(scrollyProgress)}</progress>
            </div>
        </svelte:fragment>
    </Scrolly>
</div>

<style>
    .scrolly-wrapper {
        width: min(1000ch, 60vw);
        position: relative;
        left: 50%;
        transform: translateX(-50%);

    }

    .step {
        min-height: 5vh;
        display: flex;
        flex-direction: column;
        gap: 20em;
        padding: 2rem;
    }

    .step-content {
        border-left-color: var(--color-accent);
        border-left-style: dotted;
        padding: 1.5rem 2rem;
        scroll-snap-align: start;
    }

    .project-detail {
        padding: 2rem;
        width: 100%;
    }

    .project-detail img {
        width: 100%;

    }
</style>
