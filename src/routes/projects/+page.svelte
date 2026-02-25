<script>
    import projects from "$lib/projects.json";
    import Project from "../../lib/Project.svelte";
</script>
<svelte:head>
  <title> Projects</title>
</svelte:head>
<nav>
    <li><a href=".">Home</a>
    </li>
    <li><a href="contact">Contact</a>
    </li>
    <li><a href="resume">Resume</a>
    </li>
    <li><a class="current" href="/projects">Projects</a>
    </li>
    <li><a href="https://github.com/estymagb" target="_blank">Github</a>
    </li>
</nav>

<h1>{projects.length} Projects</h1>
<div class="projects" id="projects">
{#each projects as p}
    <Project data={p}/>
{/each}
</div>

    <!----
    <article>
        <h2>Quidem sint magni animi!</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>Molestias nam, quod inventore accusamus, dolorem facere quidem voluptatem fugiat sit veritatis tempora, eos eveniet. Quo repellendus, quae adipisci temporibus similique odio? Voluptas magni enim aperiam recusandae nesciunt repellendus exercitationem.</p>
    </article>
    <article>
        <h2>Ex molestias esse totam!</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>Maxime doloremque, delectus in a reiciendis error, amet assumenda mollitia, dicta voluptates sint eius itaque consequatur nemo ducimus possimus dolore odio quo. Ipsam exercitationem distinctio officiis. Non doloremque quaerat est!</p>
    </article>
    <article>
        <h2>Aliquam doloremque temporibus iste!</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>Pariatur rem numquam mollitia dolor consequuntur! Corporis in nam repellat sapiente. Officiis, reiciendis incidunt! Veniam, dolor aut eaque ab, vero non totam dignissimos molestiae reiciendis voluptatem quod reprehenderit dolorem laborum.</p>
    </article>
    <article>
        <h2>Molestias deleniti ipsam cum.</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>Molestiae velit explicabo ut dolorem soluta exercitationem expedita harum veniam ratione nulla numquam recusandae, quam beatae itaque dicta et magni minus. Totam velit impedit, alias ratione cupiditate qui ea odit.</p>
    </article>
    <article>
        <h2>Voluptate accusamus quas voluptas?</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>Placeat facilis quasi nulla fuga soluta. Magni illo culpa, quisquam ab quia nam neque illum temporibus possimus officiis in, ipsum similique! Perspiciatis esse ad vitae necessitatibus velit! Nulla, repellendus sequi!</p>
    </article>
    <article>
        <h2>Ullam, consectetur? Similique, officia.</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>Excepturi magni ut exercitationem tempore dolores accusantium dignissimos nesciunt? Explicabo quasi possimus rerum mollitia animi ipsam, tempora fuga eligendi fugit ratione eius perferendis, deleniti eaque excepturi quaerat nam voluptatibus soluta!</p>
    </article>
    <article>
        <h2>Dicta magni numquam perspiciatis.</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>Impedit eveniet excepturi incidunt facere optio? Laborum, numquam necessitatibus praesentium eum explicabo rerum architecto, recusandae deleniti error sapiente incidunt dolores unde corrupti, nulla dicta maiores fugit porro sed. Dolor, veritatis.</p>
    </article>
    <article>
        <h2>Fugiat explicabo pariatur nesciunt!</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>Veritatis ex tempore eos in cum eaque animi id repellendus exercitationem voluptatibus assumenda natus qui, minus, libero placeat repellat dolorum mollitia voluptates quaerat fugit! In nostrum voluptatibus itaque saepe possimus?</p>
    </article>
    <article>
        <h2>Repellendus expedita aliquam aperiam!</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>Maxime voluptatem expedita neque totam rerum dignissimos eum quibusdam. Saepe cum voluptatem eum! Officia, omnis repellendus? Praesentium libero iste reiciendis obcaecati non ab eum molestias alias temporibus eveniet. Recusandae, doloremque.</p>
    </article>
    <article>
        <h2>Impedit repellendus necessitatibus fuga.</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>Sunt optio vero veniam earum quae odit maxime delectus, asperiores consectetur hic ullam? Velit, voluptatum suscipit officiis laudantium exercitationem a nostrum doloribus id ratione, dolorum officia? Nihil quod nisi sit.</p>
    </article>
    <article>
        <h2>Possimus repellendus magnam dolore.</h2>
        <img src="https://vis-society.github.io/labs/2/images/empty.svg" alt="">
        <p>Temporibus illo voluptate, commodi soluta eligendi delectus veniam in dolores eum, pariatur qui? Repellendus totam, id dolor aut recusandae architecto numquam, dolores velit iste eius modi laborum dolorum laboriosam debitis.</p>
    </article>-->
