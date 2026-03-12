<style>
    :root {
        color-scheme: light dark;
        --auto-color: light-dark(#ffffff, #121212);
    }
    nav {
        --border-color: oklch(50% 10% 200/40%);
        border-bottom: 2px solid var(--border-color);
        background-color: var(--auto-color, white);
        position: sticky;
        top: 0;
        z-index: 50;
        width: 100%;
    }
    .current {
        border-bottom: 4px solid var(--border-color);
    }

    .color-scheme-switch {
        top: 0;
        right: 0;
        width: 10%;
        position: absolute;
        display: inline-flex;
        gap: 4px;


        font-size: 80%;
        font-family: inherit;

    }
</style>

<script>
    import { base } from "$app/paths";
    import { page } from "$app/stores";
    let pages = [
        {url: "/", title: "About"},
        {url: "/projects", title: "Projects"},
        {url: "/resume", title: "Resume"},
        {url: "/contact", title: "Contact"},
        {url: "https://github.com/estymagb", title: "Github"},
        // add other pages here
    ];
    let localStorage = globalThis.localStorage ?? {};
    let colorScheme = localStorage.colorScheme ? localStorage.colorScheme :  "light dark";
    $: localStorage.colorScheme = colorScheme;

    let root = globalThis.document?.documentElement;
    $: root?.style.setProperty("color-scheme", colorScheme);

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
<label class="color-scheme-switch">
    Theme:
    <select id="color-scheme-select" bind:value={colorScheme}>
        <option value="light dark">Automatic</option>
        <option value="light">Light</option>
        <option value="dark">Dark</option>
    </select>
</label>




<slot />
