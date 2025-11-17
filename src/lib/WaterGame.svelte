<script>
  import { onMount } from 'svelte';

  let bubbles = [];
  let score = 0;
  let showScore = false;

  function createBubble() {
    const bubble = {
      id: Date.now() + Math.random(),
      x: Math.random() * (window.innerWidth - 100),
      y: window.innerHeight + 50,
      size: Math.random() * 40 + 30,
      speed: Math.random() * 2 + 1,
      wobble: Math.random() * Math.PI * 2
    };
    
    bubbles = [...bubbles, bubble];
  }

  function popBubble(id) {
    bubbles = bubbles.filter(b => b.id !== id);
    score += 10;
    showScore = true;
    setTimeout(() => showScore = false, 500);
  }

  onMount(() => {
    const interval = setInterval(() => {
      createBubble();
      
      // Update bubble positions
      bubbles = bubbles.map(bubble => ({
        ...bubble,
        y: bubble.y - bubble.speed,
        wobble: bubble.wobble + 0.05
      })).filter(bubble => bubble.y > -100);
      
    }, 2000);

    return () => clearInterval(interval);
  });
</script>

<div class="water-game">
  {#if showScore}
    <div class="score-popup">+10 💧</div>
  {/if}
  
  <div class="score-display">
    🎮 Score: {score}
  </div>

  {#each bubbles as bubble (bubble.id)}
    <button
      class="game-bubble"
      style="
        left: {bubble.x + Math.sin(bubble.wobble) * 20}px;
        bottom: {window.innerHeight - bubble.y}px;
        width: {bubble.size}px;
        height: {bubble.size}px;
      "
      on:click={() => popBubble(bubble.id)}
      aria-label="Pop bubble"
    >
      💧
    </button>
  {/each}
</div>

<style>
  .water-game {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    pointer-events: none;
    z-index: 5;
  }

  .score-display {
    position: fixed;
    top: 100px;
    right: 2rem;
    background: rgba(255, 255, 255, 0.9);
    backdrop-filter: blur(10px);
    padding: 1rem 1.5rem;
    border-radius: 30px;
    border: 3px solid var(--primary-color);
    font-weight: 700;
    color: var(--deep-blue);
    box-shadow: 0 8px 32px rgba(66, 153, 225, 0.3);
    pointer-events: none;
    animation: float 3s ease-in-out infinite;
    font-family: 'Comfortaa', sans-serif;
  }

  .score-popup {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    font-size: 3rem;
    font-weight: 700;
    color: var(--primary-color);
    animation: scorePopup 0.5s ease-out;
    pointer-events: none;
    z-index: 1000;
  }

  .game-bubble {
    position: fixed;
    pointer-events: all;
    cursor: pointer;
    border: 3px solid var(--primary-color);
    background: radial-gradient(circle at 30% 30%, rgba(190, 227, 248, 0.9), rgba(66, 153, 225, 0.7));
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 1.5rem;
    transition: all 0.2s cubic-bezier(0.68, -0.55, 0.265, 1.55);
    box-shadow: 0 4px 20px rgba(66, 153, 225, 0.4),
                inset 0 -10px 20px rgba(255, 255, 255, 0.3);
    animation: bubbleFloat 4s ease-in-out infinite;
  }

  .game-bubble:hover {
    transform: scale(1.2);
    box-shadow: 0 8px 30px rgba(66, 153, 225, 0.6);
    border-color: var(--secondary-color);
  }

  .game-bubble:active {
    animation: bubblePop 0.3s ease-out forwards;
  }

  @keyframes float {
    0%, 100% {
      transform: translateY(0px);
    }
    50% {
      transform: translateY(-10px);
    }
  }

  @keyframes scorePopup {
    0% {
      opacity: 0;
      transform: translate(-50%, -50%) scale(0.5);
    }
    50% {
      opacity: 1;
      transform: translate(-50%, -50%) scale(1.2);
    }
    100% {
      opacity: 0;
      transform: translate(-50%, -80%) scale(0.8);
    }
  }

  @keyframes bubbleFloat {
    0%, 100% {
      transform: scale(1);
    }
    50% {
      transform: scale(1.05);
    }
  }

  @keyframes bubblePop {
    0% {
      transform: scale(1);
      opacity: 1;
    }
    50% {
      transform: scale(1.3);
      opacity: 0.7;
    }
    100% {
      transform: scale(0);
      opacity: 0;
    }
  }
</style>
