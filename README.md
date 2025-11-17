# Svelte Developer Portfolio

A modern, visually stunning developer portfolio featuring an **interactive 3D animated landing page** built with Svelte and Three.js. This portfolio combines smooth scrolling effects with captivating 3D graphics to create an immersive user experience.

## Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) (v18 or higher recommended)
- [pnpm](https://pnpm.io/installation) (or npm/yarn)

### Installation

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/pr8music/dev-portfolio.git
    cd dev-portfolio
    ```

2.  **Install dependencies:**
    ```bash
    pnpm install
    ```

### Running the Development Server

To start the local development server, run:

```bash
pnpm dev
```

Your portfolio will be available at `http://localhost:5173`. The server will automatically reload when you make changes to the source files.

### Building for Production

To create a production-ready build of your site, run:

```bash
pnpm build
```

The optimized files will be generated in the `dist` directory. You can deploy this directory to any static hosting service.

## Customization

-   **`src/App.svelte`**: This is the main component. Modify it to change the overall layout and content of your portfolio.
-   **`src/lib/`**: This directory contains reusable Svelte components. Create new components here to keep your project organized.
-   **`src/styles/global.css`**: Add your own custom CSS here. No Tailwind CSS is used.

Good luck with your portfolio!

## Features

### 🎨 Interactive 3D Landing Page
The portfolio features a stunning 3D animated hero section powered by **Three.js** that includes:

- **Dynamic 3D Geometry**: Floating geometric shapes (tetrahedrons, octahedrons, icosahedrons) with individual animation paths
- **Central Wireframe Torus**: A mesmerizing rotating torus that follows mouse movements
- **Mouse Interaction**: 3D elements respond to cursor position, creating an engaging interactive experience
- **Particle System**: 50+ animated particles with varying speeds, rotations, and colors
- **Smooth Animations**: Fade-in effects for text elements with staggered timing
- **Gradient Typography**: Eye-catching gradient text that complements the 3D scene

### 🎯 Smooth Scrolling Effects
Custom smooth scroll implementation with:
- Velocity-based scrolling with easing
- Subtle skew effects based on scroll speed
- Seamless integration with 3D elements

### 📱 Fully Responsive Design
- **Desktop**: Full-featured 3D animations with optimal performance
- **Mobile**: Adapted layouts with efficient rendering for smaller devices
- **Accessibility**: Respects `prefers-reduced-motion` for users who prefer minimal animations

### ⚡ Performance Optimized
- Efficient WebGL rendering with Three.js
- Proper cleanup and resource disposal to prevent memory leaks
- Pixel ratio capping for better performance on high-DPI displays
- RequestAnimationFrame for smooth 60fps animations

## Technology Stack

- **Svelte**: Reactive UI framework
- **Three.js**: 3D graphics library for WebGL
- **Vite**: Fast build tool and development server
- **CSS3**: Modern styling with gradients, animations, and transforms

## Customization Guide

### Modifying the 3D Scene

The 3D hero section is located in `src/lib/Hero3D.svelte`. You can customize:

1. **Colors**: Update the color scheme by modifying the material colors:
   ```javascript
   const material = new THREE.MeshPhongMaterial({
     color: 0xff3e00, // Change to your preferred color
     // ...
   });
   ```

2. **Particle Count**: Adjust the number of floating shapes:
   ```javascript
   for (let i = 0; i < 50; i++) { // Change 50 to your desired count
     // ...
   }
   ```

3. **Animation Speed**: Modify the rotation and movement speeds:
   ```javascript
   mesh.userData = {
     speedX: (Math.random() - 0.5) * 0.01, // Adjust multiplier
     speedY: (Math.random() - 0.5) * 0.01,
     // ...
   };
   ```

4. **Lighting**: Customize the scene lighting in the `createCentralShape()` function:
   ```javascript
   const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
   const pointLight = new THREE.PointLight(0xff3e00, 1, 100);
   // Adjust colors and intensities
   ```

### Customizing Content

- **Hero Text**: Edit the title and subtitle in `src/lib/Hero3D.svelte`
- **About Section**: Modify content in `src/App.svelte`
- **Projects**: Update the `projects` array in `src/App.svelte`
- **Theme Colors**: Edit CSS variables in `src/styles/global.css`

### Adding New Sections

Create new components in `src/lib/` and import them into `src/App.svelte`:

```svelte
<script>
  import YourComponent from './lib/YourComponent.svelte';
</script>

<SmoothScroll>
  <Header />
  <Hero3D />
  <main>
    <!-- Your new sections here -->
  </main>
</SmoothScroll>
```

## Deployment

### Build for Production

```bash
pnpm build
```

The optimized files will be in the `dist` directory, ready to deploy to:
- **Vercel**: Connect your GitHub repo for automatic deployments
- **Netlify**: Drag and drop the `dist` folder or use continuous deployment
- **GitHub Pages**: Use GitHub Actions to deploy the `dist` folder
- **Any static hosting**: Upload the contents of `dist` to your web server

### Environment Considerations

- The 3D animations require WebGL support (available in all modern browsers)
- Initial bundle size includes Three.js (~500KB minified + gzipped ~130KB)
- Consider code-splitting for larger applications

## Performance Tips

1. **Reduce Particle Count on Mobile**: Detect mobile devices and reduce the number of 3D particles
2. **Lazy Load Three.js**: Load the 3D library only when the hero section is visible
3. **Use Production Build**: Always use `pnpm build` for production to enable optimizations
4. **Monitor Performance**: Use browser DevTools to profile and optimize as needed

## Browser Support

- ✅ Chrome/Edge (recommended for best performance)
- ✅ Firefox
- ✅ Safari
- ✅ Mobile browsers (iOS Safari, Chrome Mobile)

Requires WebGL support (available in 97%+ of browsers globally).

## Troubleshooting

### 3D Scene Not Rendering
- Check browser console for WebGL errors
- Ensure hardware acceleration is enabled in browser settings
- Try a different browser to isolate the issue

### Performance Issues
- Reduce the number of particles in `Hero3D.svelte`
- Lower the pixel ratio in the renderer setup
- Check for conflicting extensions (ad blockers, privacy tools)

### Build Warnings
- The "chunk size" warning for Three.js is normal and can be ignored for this project
- Consider implementing code-splitting if you add more heavy dependencies

## License

This project is open source and available under the MIT License.