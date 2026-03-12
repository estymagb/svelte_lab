<script>
    import projects from "$lib/projects.json";
    import reading from "$lib/reading.json"
    import Project from "../lib/Project.svelte";
    import ReadingItem from "../lib/ReadingItem.svelte";
    import { onMount } from "svelte";

    let githubData = null;
    let githubStats = null;
    let loading = true;
    let error = null;

    onMount(async() => {
         try {
            console.log("Page has been mounted!")
            // let response = await fetch("https://api.github.com/users/estymagb");
            let response = await {
                ok: true,
                json: async() => ({
                    followers: 100,
                    following: 100,
                    public_repos: 100,
                    public_gists: 1,
                }),
            };

            console.log(response);
            githubData = await response.json();
            console.log(githubData);

            githubStats = {
                "Followers": githubData.followers,
                "Following": githubData.following,
                "Public Repositories": githubData.public_repos,
                "Public Gists": githubData.public_gists,
            };
            console.log(githubStats);
        } catch (err) {
            error = err;
        }
        loading = false;
    });




</script>

<div class="homepage" >
    <h1>Esther Magbagbeola</h1>
    <div class= "intro-read">
        <div id="intro">
            <p>I am an 11-6 major, deciding to take this class to enrich my understanding of visualizing data.</p>
            <img id= "self-pic" src="images/IMG_8840.jpg"
            height="auto"
            alt="Esther Magbagbeola in Japanese Garden at MFA.">
        </div>
        <div id=reading >
            <h2>What I'm Reading</h2>
            {#each reading as r}
            <ReadingItem data ={r}/>
            {/each}
        </div>
    </div>

    {#if loading}
    <p>Loading...</p>
    {:else if error}
        <p>Something went wrong: {error.message}</p>
    {:else}
        <section>
            <h2>My GitHub Stats</h2>
            <div class="github-stats">
                {#each Object.entries(githubStats) as [name, value] (name)}
                <div id=github-stat-attribute>
                    <dt>{name}:</dt>
                    <dd>{value}</dd>
                </div>
                {/each}
            </div>
        </section>
    {/if}


    <h2>Latest Projects</h2>
    <div class="projects highlights" >
        {#each projects.slice(0,3) as p}
        <Project data={p} />
        {/each}
    </div>
</div>

<style>
    :root {
        --light-dark-reverse: light-dark(#121212, #ffffff)
    }

    .homepage h1 {
        font-size: auto;
    }

    .homepage h2 {
        color: deeppink;
        font-size: 200%;
        text-decoration: underline;
    }

    .intro-read {
        display: flex;
        gap: 1em;
    }

    .intro-read h2 {
        font: auto;
        font-size: auto;
        color: var(--light-dark-reverse);
    }

    .github-stats {
        display: grid;
        grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
        grid-column: 1 /-1;
        border-style: solid;

        place-content: center;
        box-shadow: 0 0 3px 1px #cccccc;

    }

    #github-stat-attribute {
        padding: 1.0em;
        text-transform: uppercase;
    }

    #github-stat-attribute dt {
        text-align: center;
        font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
        text-decoration: underline;
        font-size: 80%;
    }
    #github-stat-attribute dd {
        text-align: auto;
        font-size: x-large;
    }


    #reading {

        /**Styling*/
        font-size: small;
        background-color: rgb(250, 178, 203);

        /**Box work*/
        box-sizing: border-box;
        min-width: fit-content;
        max-height: fit-content;
        min-height: fit-content;
        border-radius: 15px;
        align-items: center;
        padding: 1.5em;

        /**Organize spacing between entries*/
        padding-bottom: auto;
    }

</style>
