<svelte:head>
    <title>Larissa's page</title>
</svelte:head>

<script>
    import projects from "$lib/projects.json";
    import Project from "$lib/Project.svelte";
    import { onMount } from "svelte";

    let githubData = null;
    let loading = true;
    let error = null;

    onMount(async () => {
        try {
            const response = await fetch("https://api.github.com/users/LarissaLAfonso");
            githubData = await response.json();
        } catch (err) {
            error = err;
        }
        loading = false;
    });


</script>

<h1>Larissa Lemos Afonso</h1>
    

<!-- Specifies the file path (source); sets alternative text in case the image doesn't show-->
<img src = "images/medievalgirl.jpg" alt = "Myself, some hundreds of years ago" height="300">

<p>I'm majoring in data science and artificial intelligence at FGV EMAp.</p>
<p>I like statistics, working out and Japanese language/culture :)</p>

<h3>Latest projects</h3>
<div class="projects">
    {#each projects.slice(0, 3) as p}
        <Project data={p} hLevel="3"/>
    {/each}

    {#if loading}
        <p>Loading...</p>
    {:else if error}
        <p class="error">Something went wrong: {error.message}</p>
    {:else}
        <section>
            <h2>My GitHub Stats</h2>
            <dl>
                <dt>Followers</dt>
                <dd>{githubData.followers}</dd>
                <dt>Following</dt>
                <dd>{githubData.following}</dd>
                <dt>Public Repositories</dt>
                <dd>{githubData.public_repos}</dd>
            </dl>
        </section>
    {/if}

</div>