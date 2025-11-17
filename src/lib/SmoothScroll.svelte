<script>
  import { onMount, onDestroy } from 'svelte';

  let scrollable;
  let height;
  let skew = 0;

  let y = 0;
  let speed = 0.08;
  let offset = 0;

  const update = () => {
    offset += (y - offset) * speed;
    
    // Add a little skew effect based on scroll speed
    let diff = y - offset;
    skew = Math.min(Math.max(diff * 0.015, -2), 2);

    if (scrollable) {
      scrollable.style.transform = `translateY(-${offset}px) skewY(${skew}deg)`;
    }
    
    requestAnimationFrame(update);
  }

  onMount(() => {
    update();
  });

  function handleResize() {
    height = scrollable.clientHeight;
    document.body.style.height = `${height}px`;
  }

  function handleScroll() {
    y = window.scrollY;
  }
</script>

<svelte:window on:scroll={handleScroll} on:resize={handleResize}/>

<div class="scrollable" bind:this={scrollable}>
  <slot></slot>
</div>

<style>
  .scrollable {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    will-change: transform;
    transform-origin: center center;
  }
</style>
