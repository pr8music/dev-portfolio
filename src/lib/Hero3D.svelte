<script>
  import { onMount, onDestroy } from 'svelte';
  import * as THREE from 'three';

  let canvas;
  let scene, camera, renderer;
  let particles = [];
  let mouseX = 0;
  let mouseY = 0;
  let targetX = 0;
  let targetY = 0;
  let animationId;

  onMount(() => {
    initScene();
    animate();
    window.addEventListener('mousemove', handleMouseMove);
    window.addEventListener('resize', handleResize);
  });

  onDestroy(() => {
    window.removeEventListener('mousemove', handleMouseMove);
    window.removeEventListener('resize', handleResize);
    if (animationId) {
      cancelAnimationFrame(animationId);
    }
    // Proper cleanup
    if (renderer) {
      renderer.dispose();
    }
    if (scene) {
      scene.traverse((object) => {
        if (object.geometry) object.geometry.dispose();
        if (object.material) {
          if (Array.isArray(object.material)) {
            object.material.forEach(material => material.dispose());
          } else {
            object.material.dispose();
          }
        }
      });
    }
  });

  function initScene() {
    // Scene setup
    scene = new THREE.Scene();
    scene.fog = new THREE.Fog(0xf0f7ff, 1, 15);

    // Camera setup
    camera = new THREE.PerspectiveCamera(
      75,
      window.innerWidth / window.innerHeight,
      0.1,
      1000
    );
    camera.position.z = 5;

    // Renderer setup
    renderer = new THREE.WebGLRenderer({ 
      canvas, 
      alpha: true,
      antialias: true 
    });
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

    // Create floating water droplets/bubbles
    createParticles();
    createCentralShape();
  }

  function createParticles() {
    // Create water droplets/bubbles instead of geometric shapes
    for (let i = 0; i < 80; i++) {
      const radius = Math.random() * 0.15 + 0.05;
      const geometry = new THREE.SphereGeometry(radius, 16, 16);
      
      // Water bubble material with transparency
      const material = new THREE.MeshPhongMaterial({
        color: i % 4 === 0 ? 0x4299e1 : i % 4 === 1 ? 0x63b3ed : i % 4 === 2 ? 0x90cdf4 : 0xbee3f8,
        shininess: 100,
        specular: 0xffffff,
        transparent: true,
        opacity: 0.7 + Math.random() * 0.3,
        emissive: 0x4299e1,
        emissiveIntensity: 0.1
      });

      const mesh = new THREE.Mesh(geometry, material);
      mesh.position.x = (Math.random() - 0.5) * 10;
      mesh.position.y = (Math.random() - 0.5) * 10;
      mesh.position.z = (Math.random() - 0.5) * 10;

      mesh.userData = {
        speedX: (Math.random() - 0.5) * 0.008,
        speedY: Math.random() * 0.015 + 0.005, // Float upward like bubbles
        originalY: mesh.position.y,
        bobSpeed: Math.random() * 0.02 + 0.01,
        bobAmount: Math.random() * 0.3 + 0.2,
        phase: Math.random() * Math.PI * 2
      };

      scene.add(mesh);
      particles.push(mesh);
    }
  }

  function createCentralShape() {
    // Create a main liquid blob/droplet shape
    const geometry = new THREE.SphereGeometry(1.2, 32, 32);
    const material = new THREE.MeshPhongMaterial({
      color: 0x4299e1,
      shininess: 100,
      specular: 0xffffff,
      transparent: true,
      opacity: 0.6,
      emissive: 0x63b3ed,
      emissiveIntensity: 0.2
    });
    const sphere = new THREE.Mesh(geometry, material);
    sphere.userData.isCentral = true;
    scene.add(sphere);
    particles.push(sphere);

    // Add lighting for water effect
    const ambientLight = new THREE.AmbientLight(0xffffff, 0.6);
    scene.add(ambientLight);

    const pointLight = new THREE.PointLight(0x4299e1, 1.5, 100);
    pointLight.position.set(3, 3, 3);
    scene.add(pointLight);

    const pointLight2 = new THREE.PointLight(0x90cdf4, 1, 100);
    pointLight2.position.set(-3, -2, 3);
    scene.add(pointLight2);

    const pointLight3 = new THREE.PointLight(0xbee3f8, 0.8, 100);
    pointLight3.position.set(0, -3, 2);
    scene.add(pointLight3);
  }

  function handleMouseMove(event) {
    targetX = (event.clientX / window.innerWidth) * 2 - 1;
    targetY = -(event.clientY / window.innerHeight) * 2 + 1;
  }

  function handleResize() {
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
  }

  function animate() {
    animationId = requestAnimationFrame(animate);

    // Smooth mouse following
    mouseX += (targetX - mouseX) * 0.05;
    mouseY += (targetY - mouseY) * 0.05;

    // Animate particles
    particles.forEach((particle) => {
      if (particle.userData.isCentral) {
        // Central water blob - gentle pulsing and floating
        const time = Date.now() * 0.001;
        particle.scale.x = 1 + Math.sin(time * 2) * 0.1;
        particle.scale.y = 1 + Math.sin(time * 2.5) * 0.1;
        particle.scale.z = 1 + Math.sin(time * 2.2) * 0.1;
        particle.position.x = mouseX * 0.5;
        particle.position.y = mouseY * 0.5 + Math.sin(time) * 0.2;
        particle.rotation.y += 0.005;
      } else {
        // Water droplets - float upward with bobbing motion
        particle.position.y += particle.userData.speedY;
        particle.position.x += particle.userData.speedX;
        
        // Add bobbing/wobbling motion
        const time = Date.now() * 0.001;
        particle.position.x += Math.sin(time * particle.userData.bobSpeed + particle.userData.phase) * 0.01;
        particle.position.z += Math.cos(time * particle.userData.bobSpeed + particle.userData.phase) * 0.01;

        // Reset position when bubble floats too high (like real bubbles)
        if (particle.position.y > 6) {
          particle.position.y = -6;
          particle.position.x = (Math.random() - 0.5) * 10;
        }
        
        // Wrap around horizontally
        if (particle.position.x > 5) particle.position.x = -5;
        if (particle.position.x < -5) particle.position.x = 5;

        // Mouse interaction - bubbles avoid mouse (like water surface tension)
        const dx = mouseX * 2 - particle.position.x;
        const dy = mouseY * 2 - particle.position.y;
        const distance = Math.sqrt(dx * dx + dy * dy);
        
        if (distance < 2) {
          particle.position.x -= dx * 0.03;
          particle.position.y -= dy * 0.03;
          // Slight scale change when interacting
          particle.scale.setScalar(1 + (2 - distance) * 0.2);
        } else {
          // Return to normal scale
          particle.scale.lerp(new THREE.Vector3(1, 1, 1), 0.1);
        }
      }
    });

    // Camera movement based on mouse
    camera.position.x = mouseX * 0.3;
    camera.position.y = mouseY * 0.3;
    camera.lookAt(scene.position);

    renderer.render(scene, camera);
  }
</script>

<div class="hero-container">
  <canvas bind:this={canvas}></canvas>
  <div class="hero-content">
    <h1 class="hero-title">
      <span class="title-line">Pranavvetrivel</span>
      <span class="title-line">Balaji</span>
      <span class="title-line">A Portfolio</span>
    </h1>
    <p class="hero-subtitle">
      Crafting immersive digital experiences with cutting-edge technology
    </p>
    <div class="scroll-indicator">
      <span>Scroll to explore</span>
      <div class="scroll-arrow">↓</div>
    </div>
  </div>
</div>

<style>
  .hero-container {
    position: relative;
    width: 100%;
    height: 100vh;
    overflow: hidden;
    background: linear-gradient(180deg, #e6f2ff 0%, #f0f7ff 50%, #e0f4ff 100%);
  }

  canvas {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 1;
  }

  .hero-content {
    position: relative;
    z-index: 2;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    text-align: center;
    padding: 2rem;
  }

  .hero-title {
    font-size: clamp(2.5rem, 8vw, 6rem);
    font-weight: 700;
    margin: 0;
    line-height: 1.1;
    display: flex;
    flex-direction: column;
    gap: 0.2rem;
  }

  .title-line {
    display: block;
    background: linear-gradient(135deg, #4299e1 0%, #63b3ed 50%, #90cdf4 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
    animation: fadeInUp 0.8s ease-out backwards, floatTitle 3s ease-in-out infinite;
    text-shadow: 2px 2px 20px rgba(66, 153, 225, 0.3);
    filter: drop-shadow(0 4px 8px rgba(66, 153, 225, 0.2));
  }

  .title-line:nth-child(1) {
    animation-delay: 0.2s;
  }

  .title-line:nth-child(2) {
    animation-delay: 0.4s;
  }

  .title-line:nth-child(3) {
    animation-delay: 0.6s;
    font-size: 0.8em;
    background: linear-gradient(135deg, #90cdf4 0%, #bee3f8 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
  }

  .hero-subtitle {
    font-size: clamp(1rem, 2vw, 1.5rem);
    color: var(--deep-blue);
    margin-top: 2rem;
    max-width: 600px;
    opacity: 0;
    animation: fadeIn 1s ease-out 0.8s forwards;
    font-weight: 500;
  }

  .scroll-indicator {
    position: absolute;
    bottom: 3rem;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0.5rem;
    color: var(--primary-color);
    opacity: 0;
    animation: fadeIn 1s ease-out 1.2s forwards;
  }

  .scroll-indicator span {
    font-size: 0.875rem;
    text-transform: uppercase;
    letter-spacing: 2px;
    font-weight: 600;
  }

  .scroll-arrow {
    font-size: 1.5rem;
    animation: bounce 2s infinite;
    filter: drop-shadow(0 2px 4px rgba(66, 153, 225, 0.3));
  }

  @keyframes floatTitle {
    0%, 100% {
      transform: translateY(0px);
    }
    50% {
      transform: translateY(-10px);
    }
  }

  @keyframes fadeInUp {
    from {
      opacity: 0;
      transform: translateY(30px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }

  @keyframes fadeIn {
    from {
      opacity: 0;
    }
    to {
      opacity: 1;
    }
  }

  @keyframes bounce {
    0%, 20%, 50%, 80%, 100% {
      transform: translateY(0);
    }
    40% {
      transform: translateY(-10px);
    }
    60% {
      transform: translateY(-5px);
    }
  }

  /* Responsive adjustments */
  @media (max-width: 768px) {
    .hero-content {
      padding: 1rem;
    }

    .hero-subtitle {
      font-size: 1rem;
      margin-top: 1.5rem;
    }

    .scroll-indicator {
      bottom: 2rem;
    }
  }

  /* Reduce motion for accessibility */
  @media (prefers-reduced-motion: reduce) {
    .title-line,
    .hero-subtitle,
    .scroll-indicator {
      animation: none;
      opacity: 1;
    }

    .scroll-arrow {
      animation: none;
    }
  }
</style>
