<script>
    import { base } from "$app/paths";
    import { page } from "$app/stores";
    
    // Initialize localStorage safely
    let localStorage = globalThis.localStorage ?? {};
    // Get initial color scheme from localStorage
    let colorScheme = localStorage.colorScheme ?? "light dark";

    let pages = [
        {url: "/", title: "Home"},
        {url: "/projects", title: "Projects"},
        {url: "/contact", title: "Contact"},
        {url: "https://github.com/LarissaLAfonso", title: "GitHub"}
    ];

    // Reactive statement to apply color scheme
    $: {
        const root = globalThis.document?.documentElement;
        root?.style.setProperty("color-scheme", colorScheme);
    }

    // Reactive statement to save to localStorage
    $: {
        if (colorScheme) {
            localStorage.colorScheme = colorScheme;
        }
    }
</script>

<nav>
    {#each pages as p}
    <a
    href={p.url.startsWith("http") ? p.url: `${base}${p.url}`}
    class:current={$page.route.id === p.url}
    target={p.url.startsWith("http") ? "_blank" : undefined}
>
    {p.title}
</a>


    {/each}
</nav>

<label class="color-scheme">
    Theme:
    <select bind:value={colorScheme}>
        <option value="light dark"> Automatic </option>
        <option value="light"> Light </option>
        <option value="dark"> Dark </option>
    </select>
</label>

<slot />

<style>
    nav {
    display: flex;
    border-bottom-color: var(--color-accent);
    background-color: color-mix(in oklch, var(--color-accent), canvas 85%);
    }

    nav a {
        flex:1; /*All elements take up the same amount of space*/
        text-decoration: none;
        color: inherit;
        text-align: center;
        padding: 0.5em;
        margin-bottom: 0.01em;
        border-bottom-width: 1px;
        border-bottom-style: solid;
        border-bottom-color : var(--border-color);
    }

    nav a.current {
        border-bottom-width:0.4em;
        border-bottom-style:solid;
        border-bottom-color:oklch(80% 3% 200);
        padding-bottom:0.1em;
        font-weight: bold;
    }

    nav a:hover {
        border-bottom-width:0.4em;
        border-bottom-style:solid;
        border-bottom-color:var(--color-accent);
        padding-bottom:0.1em;
        background-color: oklch(from var(--color-accent) 95% 5% h);
    }

    :root {
    --grey: oklch(93.62% 0 360);
    --white: #ffffff;
    --color-accent: oklch(87.94% 0.0617 19.76);
    --border-color: oklch(50% 10% 200 / 40%);
    }

    .color-scheme {
        position: absolute;
        top: 1rem;
        right: 1rem;
        display: flex;
        align-items: center;
        gap: 0.5rem;
    }

    select {
        padding: 4px;
        background-color: var(--white);
        font-size: 1rem;
        line-height: 1.5;
    }
</style>