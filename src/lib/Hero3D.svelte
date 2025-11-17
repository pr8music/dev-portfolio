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
    scene.fog = new THREE.Fog(0x1a1a1a, 1, 15);

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

    // Create floating geometric shapes
    createParticles();
    createCentralShape();
  }

  function createParticles() {
    const geometries = [
      new THREE.TetrahedronGeometry(0.1),
      new THREE.OctahedronGeometry(0.1),
      new THREE.IcosahedronGeometry(0.1)
    ];

    for (let i = 0; i < 50; i++) {
      const geometry = geometries[Math.floor(Math.random() * geometries.length)];
      const material = new THREE.MeshPhongMaterial({
        color: i % 3 === 0 ? 0xff3e00 : i % 3 === 1 ? 0xff6633 : 0xff8855,
        shininess: 100,
        specular: 0x555555,
        wireframe: Math.random() > 0.5
      });

      const mesh = new THREE.Mesh(geometry, material);
      mesh.position.x = (Math.random() - 0.5) * 10;
      mesh.position.y = (Math.random() - 0.5) * 10;
      mesh.position.z = (Math.random() - 0.5) * 10;
      
      mesh.rotation.x = Math.random() * Math.PI;
      mesh.rotation.y = Math.random() * Math.PI;

      mesh.userData = {
        speedX: (Math.random() - 0.5) * 0.01,
        speedY: (Math.random() - 0.5) * 0.01,
        rotationSpeedX: (Math.random() - 0.5) * 0.02,
        rotationSpeedY: (Math.random() - 0.5) * 0.02
      };

      scene.add(mesh);
      particles.push(mesh);
    }
  }

  function createCentralShape() {
    // Create a main central rotating torus
    const torusGeometry = new THREE.TorusGeometry(1, 0.3, 16, 100);
    const torusMaterial = new THREE.MeshPhongMaterial({
      color: 0xff3e00,
      shininess: 100,
      specular: 0x888888,
      wireframe: true
    });
    const torus = new THREE.Mesh(torusGeometry, torusMaterial);
    torus.userData.isCentral = true;
    scene.add(torus);
    particles.push(torus);

    // Add lighting
    const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
    scene.add(ambientLight);

    const pointLight = new THREE.PointLight(0xff3e00, 1, 100);
    pointLight.position.set(2, 2, 2);
    scene.add(pointLight);

    const pointLight2 = new THREE.PointLight(0x00aaff, 0.5, 100);
    pointLight2.position.set(-2, -2, 2);
    scene.add(pointLight2);
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
        // Central torus animation
        particle.rotation.x += 0.005;
        particle.rotation.y += 0.01;
        particle.position.x = mouseX * 0.5;
        particle.position.y = mouseY * 0.5;
      } else {
        // Other particles
        particle.position.x += particle.userData.speedX;
        particle.position.y += particle.userData.speedY;
        particle.rotation.x += particle.userData.rotationSpeedX;
        particle.rotation.y += particle.userData.rotationSpeedY;

        // Wrap around screen
        if (particle.position.x > 5) particle.position.x = -5;
        if (particle.position.x < -5) particle.position.x = 5;
        if (particle.position.y > 5) particle.position.y = -5;
        if (particle.position.y < -5) particle.position.y = 5;

        // Mouse interaction
        const dx = mouseX * 2 - particle.position.x;
        const dy = mouseY * 2 - particle.position.y;
        const distance = Math.sqrt(dx * dx + dy * dy);
        
        if (distance < 2) {
          particle.position.x -= dx * 0.01;
          particle.position.y -= dy * 0.01;
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
      <span class="title-line">Creative</span>
      <span class="title-line">Developer</span>
      <span class="title-line">Portfolio</span>
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
    background: linear-gradient(180deg, #1a1a1a 0%, #0d0d0d 100%);
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
    background: linear-gradient(135deg, #ff3e00 0%, #ff8855 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
    animation: fadeInUp 0.8s ease-out backwards;
  }

  .title-line:nth-child(1) {
    animation-delay: 0.2s;
  }

  .title-line:nth-child(2) {
    animation-delay: 0.4s;
  }

  .title-line:nth-child(3) {
    animation-delay: 0.6s;
  }

  .hero-subtitle {
    font-size: clamp(1rem, 2vw, 1.5rem);
    color: var(--text-color);
    margin-top: 2rem;
    max-width: 600px;
    opacity: 0;
    animation: fadeIn 1s ease-out 0.8s forwards;
  }

  .scroll-indicator {
    position: absolute;
    bottom: 3rem;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0.5rem;
    color: var(--text-color);
    opacity: 0;
    animation: fadeIn 1s ease-out 1.2s forwards;
  }

  .scroll-indicator span {
    font-size: 0.875rem;
    text-transform: uppercase;
    letter-spacing: 2px;
  }

  .scroll-arrow {
    font-size: 1.5rem;
    animation: bounce 2s infinite;
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
