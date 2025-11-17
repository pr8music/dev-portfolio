# Svelte Developer Portfolio

This is a starter template for a visually interesting and interactive developer portfolio, built with Svelte.

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