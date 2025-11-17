<script>
  export let name;
  export let description;
</script>

<div class="card">
  <h3>{name}</h3>
  <p>{description}</p>
</div>

<style>
  .card {
    background: linear-gradient(135deg, rgba(255, 255, 255, 0.9) 0%, rgba(224, 244, 255, 0.9) 100%);
    padding: 2rem;
    border-radius: 30px;
    transition: all 0.4s cubic-bezier(0.68, -0.55, 0.265, 1.55);
    border: 3px solid var(--accent-color);
    box-shadow: 0 8px 32px rgba(66, 153, 225, 0.2);
    position: relative;
    overflow: hidden;
    cursor: pointer;
  }

  .card::before {
    content: '';
    position: absolute;
    top: -50%;
    left: -50%;
    width: 200%;
    height: 200%;
    background: radial-gradient(circle, rgba(190, 227, 248, 0.4) 0%, transparent 70%);
    transform: scale(0);
    transition: transform 0.6s ease-out;
    border-radius: 50%;
  }

  .card:hover::before {
    transform: scale(1);
  }

  .card:hover {
    transform: translateY(-10px) scale(1.02);
    box-shadow: 0 16px 48px rgba(66, 153, 225, 0.3);
    border-color: var(--primary-color);
    animation: wobble 0.5s ease-in-out;
  }

  h3 {
    margin-top: 0;
    color: var(--deep-blue);
    font-size: 1.5rem;
    position: relative;
    z-index: 1;
  }

  p {
    position: relative;
    z-index: 1;
    color: var(--text-color);
    line-height: 1.6;
  }

  @keyframes wobble {
    0%, 100% {
      transform: translateY(-10px) scale(1.02) rotate(0deg);
    }
    25% {
      transform: translateY(-10px) scale(1.02) rotate(-2deg);
    }
    75% {
      transform: translateY(-10px) scale(1.02) rotate(2deg);
    }
  }
</style>
