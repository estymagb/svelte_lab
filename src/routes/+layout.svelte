<script>
    import { base } from "$app/paths";
    import {page } from "$app/stores";
    let pages = [
        {url: "/", title: "About"},
        {url: "/projects", title: "Projects"},
        {url: "/resume", title: "Resume"},
        {url: "/contact", title: "Contact"},
        {url: "https://github.com/estymagb", title: "Github"},
        // add other pages here
    ];
</script>
<nav>
    {#each pages as p}
        <a href={!p.url.startsWith("http") ? base + p.url : "" + p.url}
        class:current={p.url === "/"
        ? $page.url.pathname === (base + "/")
        : $page.url.pathname.startsWith(base + p.url) }
        target={p.url.startsWith("http") ? "_blank": null}
        >
            {p.title}
        </a>
    {/each}
</nav>



<slot />
