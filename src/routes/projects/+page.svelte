<svelte:head>
    <title>Projects</title>
</svelte:head>

<script>
    import projects from "$lib/projects.json";
    import Project from "$lib/Project.svelte";
    import Pie from '$lib/Pie.svelte';
</script>

<Pie/>

<h1>My projects</h1>
<div class="projects">
    {#each projects as p} 
      <Project data={p} />
    {/each}
  </div>