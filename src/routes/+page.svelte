<svelte:head>
    <title>Larissa's page</title>
</svelte:head>

<script>
    import projects from "$lib/projects.json";
    import Project from "$lib/Project.svelte";
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
</div>