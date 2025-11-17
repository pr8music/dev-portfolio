<script>
  import Header from './lib/Header.svelte';
  import Hero3D from './lib/Hero3D.svelte';
  import ProjectCard from './lib/ProjectCard.svelte';
  import SmoothScroll from './lib/SmoothScroll.svelte';

  const projects = [
    { name: 'Project One', description: 'An interesting project I built. It does X, Y, and Z. The tech stack was A, B, and C.' },
    { name: 'Project Two', description: 'Another cool project. This one focuses on creating a unique user interaction.' },
    { name: 'Project Three', description: 'A collaborative project that solved a complex problem for a client.' }
  ];
</script>

<SmoothScroll>
  <Header />
  <Hero3D />

  <main>
    <section id="about">
      <h1>About Me</h1>
      <p>
        I'm a passionate developer who loves creating interactive and visually appealing web experiences.
        This portfolio is a showcase of my journey and skills.
      </p>
    </section>

    <section id="projects">
      <h2>My Projects</h2>
      <div class="project-grid">
        {#each projects as project}
          <ProjectCard name={project.name} description={project.description} />
        {/each}
      </div>
    </section>

    <section id="contact">
      <h2>Get In Touch</h2>
      <p>
        I'm always open to new opportunities and collaborations. Feel free to reach out!
        <br />
        <a href="mailto:your-email@example.com">your-email@example.com</a>
      </p>
    </section>
  </main>
</SmoothScroll>

<style>
  .project-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
    gap: 2rem;
  }
  a {
    color: var(--primary-color);
    text-decoration: none;
    transition: color 0.2s;
  }
  a:hover {
    text-decoration: underline;
  }
</style>
