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
    background: rgba(255, 255, 255, 0.85);
    backdrop-filter: blur(10px);
    z-index: 10;
    border-bottom: 3px solid var(--accent-color);
    box-shadow: 0 4px 20px rgba(66, 153, 225, 0.1);
  }

  nav {
    display: flex;
    justify-content: center;
    gap: 2rem;
  }

  a {
    color: var(--primary-color);
    text-decoration: none;
    font-weight: 700;
    transition: all 0.3s cubic-bezier(0.68, -0.55, 0.265, 1.55);
    padding: 0.5rem 1.5rem;
    border-radius: 50px;
    position: relative;
    overflow: hidden;
  }

  a::before {
    content: '';
    position: absolute;
    top: 50%;
    left: 50%;
    width: 0;
    height: 0;
    border-radius: 50%;
    background: var(--bubble-blue);
    transform: translate(-50%, -50%);
    transition: width 0.4s, height 0.4s;
    z-index: -1;
  }

  a:hover {
    color: var(--deep-blue);
    transform: translateY(-3px) scale(1.05);
  }

  a:hover::before {
    width: 200%;
    height: 200%;
  }
</style>
