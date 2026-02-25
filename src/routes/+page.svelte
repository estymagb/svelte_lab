<script>
    import projects from "$lib/projects.json";
    import reading from "$lib/reading.json"
    import Project from "../lib/Project.svelte";
    import ReadingItem from "../lib/ReadingItem.svelte";
</script>
<nav>
    <li><a class="current" href=".">Home</a>
    </li>
    <li><a href="contact">Contact</a>
    </li>
    <li><a href="resume">Resume</a>
    </li>
    <li><a href="projects">Projects</a>
    </li>
    <li><a href="https://github.com/estymagb" target="_blank">Github</a>
    </li>
</nav>
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

    <h2>Latest Projects</h2>
    <div class="projects highlights" >
        {#each projects.slice(0,3) as p}
        <Project data={p} />
        {/each}
    </div>
</div>

<style>
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
        color: black;
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
