<script>
  import Header from './lib/Header.svelte';
  import Hero3D from './lib/Hero3D.svelte';
  import ProjectCard from './lib/ProjectCard.svelte';
  import SmoothScroll from './lib/SmoothScroll.svelte';
  import WaterGame from './lib/WaterGame.svelte';

  const projects = [
    { name: 'Project One', description: 'An interesting project I built. It does X, Y, and Z. The tech stack was A, B, and C.' },
    { name: 'Project Two', description: 'Another cool project. This one focuses on creating a unique user interaction.' },
    { name: 'Project Three', description: 'A collaborative project that solved a complex problem for a client.' }
  ];

  let ripples = [];

  function createRipple(event) {
    const x = event.clientX;
    const y = event.clientY;
    
    ripples = [...ripples, { x, y, id: Date.now() }];
    
    setTimeout(() => {
      ripples = ripples.slice(1);
    }, 1000);
  }
</script>

<svelte:window on:click={createRipple} />

<WaterGame />

<SmoothScroll>
  <Header />
  <Hero3D />

  <main>
    <section id="about">
      <div class="water-section">
        <h1>About Me 🌊</h1>
        <p>
          I'm a passionate developer who loves creating interactive and visually appealing web experiences.
          This portfolio is a showcase of my journey and skills.
        </p>
      </div>
    </section>

    <section id="projects">
      <div class="water-section">
        <h2>My Projects 💧</h2>
        <div class="project-grid">
          {#each projects as project}
            <ProjectCard name={project.name} description={project.description} />
          {/each}
        </div>
      </div>
    </section>

    <section id="contact">
      <div class="water-section">
        <h2>Get In Touch 🐟</h2>
        <p>
          I'm always open to new opportunities and collaborations. Feel free to reach out!
          <br />
          <a href="mailto:your-email@example.com" class="water-link">your-email@example.com</a>
        </p>
      </div>
    </section>
  </main>

  <!-- Water ripple effects -->
  <div class="ripple-container">
    {#each ripples as ripple (ripple.id)}
      <div 
        class="ripple" 
        style="left: {ripple.x}px; top: {ripple.y}px;"
      ></div>
    {/each}
  </div>
</SmoothScroll>

<style>
  .project-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
    gap: 2rem;
  }

  .water-section {
    padding: 2rem;
    background: rgba(255, 255, 255, 0.6);
    backdrop-filter: blur(10px);
    border-radius: 40px;
    border: 3px solid var(--accent-color);
    box-shadow: 0 8px 32px rgba(66, 153, 225, 0.15);
    animation: float 6s ease-in-out infinite;
  }

  h1, h2 {
    background: linear-gradient(135deg, var(--primary-color) 0%, var(--secondary-color) 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
    filter: drop-shadow(0 2px 4px rgba(66, 153, 225, 0.2));
  }

  a {
    color: var(--primary-color);
    text-decoration: none;
    transition: all 0.3s;
    font-weight: 600;
    position: relative;
    padding: 0.5rem 1rem;
    border-radius: 20px;
    display: inline-block;
  }

  a:hover {
    background: var(--bubble-blue);
    transform: scale(1.05);
    box-shadow: 0 4px 12px rgba(66, 153, 225, 0.3);
  }

  .ripple-container {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    pointer-events: none;
    z-index: 9999;
  }

  .ripple {
    position: absolute;
    width: 20px;
    height: 20px;
    border-radius: 50%;
    background: radial-gradient(circle, rgba(66, 153, 225, 0.6) 0%, transparent 70%);
    border: 2px solid var(--primary-color);
    transform: translate(-50%, -50%);
    animation: ripple 1s ease-out forwards;
    pointer-events: none;
  }

  @keyframes ripple {
    0% {
      transform: translate(-50%, -50%) scale(0);
      opacity: 1;
    }
    100% {
      transform: translate(-50%, -50%) scale(20);
      opacity: 0;
    }
  }
</style>
