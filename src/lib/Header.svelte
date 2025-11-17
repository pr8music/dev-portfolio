<header>
  <nav>
    <a href="#about">About</a>
    <a href="#projects">Projects</a>
    <a href="#contact">Contact</a>
  </nav>
</header>

<style>
  header {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    padding: 1rem 2rem;
    background: rgba(26, 26, 26, 0.8);
    backdrop-filter: blur(10px);
    z-index: 10;
  }

  nav {
    display: flex;
    justify-content: center;
    gap: 2rem;
  }

  a {
    color: var(--text-color);
    text-decoration: none;
    font-weight: 700;
    transition: color 0.3s;
  }

  a:hover {
    color: var(--primary-color);
  }
</style>
