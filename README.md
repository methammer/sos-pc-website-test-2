# SOS-PC Website

Welcome to the official repository for the SOS-PC website. This project aims to create a modern, responsive, and informative online presence for SOS-PC, a provider of personalized IT and web solutions.

## ✨ About SOS-PC

SOS-PC, led by a passionate self-employed entrepreneur, offers a comprehensive range of services to meet diverse technological needs. The goal is to provide reliable, high-performance, and tailored solutions for both individuals and professionals.

### Key Services Highlighted:
*   **Réparation Informatique:** Troubleshooting, custom PC builds, optimization, and data recovery.
*   **Création de Sites Web:** Modern, responsive showcase websites optimized for online presence.
*   **Hébergement Streaming:** Setup and management of personal media platforms (e.g., Jellyfin).
*   **Hébergement Modèles IA (In Development):** Deployment and management of AI models.

## 🚀 Project Goals

*   Develop a professional and engaging website using [Astro](https://astro.build/).
*   Showcase SOS-PC's services and expertise.
*   Provide an easy way for potential clients to get in touch.
*   Ensure a responsive design for optimal viewing on all devices.
*   Incorporate interactive elements like a service showcase and a project vitrine.

## 🛠️ Tech Stack

*   **Framework:** [Astro](https://astro.build/) - For building fast, content-focused websites.
*   **Styling:** CSS (with CSS Variables for theming).
*   **JavaScript:** For interactive components (e.g., Swiper.js for carousels).
*   **Deployment:** (To be determined - currently set up for local development)

## ⚙️ Getting Started

To get a local copy up and running, follow these simple steps.

### Prerequisites

*   Node.js (version 18.x or higher recommended)
*   npm (comes with Node.js)

### Installation & Running

1.  **Clone the repository (if applicable):**
    If you have this project locally, you can skip this step.
    ```sh
    # git clone https://your-repository-url.git
    # cd SOS-PC-website-logo-test-main
    ```

2.  **Install NPM packages:**
    This will install Astro and other necessary dependencies.
    ```sh
    npm install
    ```

3.  **Run the development server:**
    This command starts the local development server, typically at `http://localhost:4321`.
    ```sh
    npm run dev
    ```
    Astro will automatically open the site in your default web browser.

### Other Available Scripts

*   **Build for production:**
    ```sh
    npm run build
    ```
    This command builds the static site to the `./dist/` directory.

*   **Preview the production build:**
    ```sh
    npm run preview
    ```
    This command serves the `./dist/` folder locally to preview the production build.

*   **Astro CLI commands:**
    You can run various Astro CLI commands directly.
    ```sh
    npm run astro -- --help
    ```

## 📁 Project Structure

The project follows a standard Astro project structure:

```
/
├── public/             # Static assets (images, favicons, etc.)
│   └── favicon.svg
├── src/
│   ├── components/     # Reusable Astro components (e.g., Navbar.astro, ThemeSwitcher.astro)
│   ├── layouts/        # Base layout components (e.g., Layout.astro)
│   ├── pages/          # Astro pages, which become routes (e.g., index.astro)
│   └── env.d.ts        # TypeScript environment declarations
├── astro.config.mjs    # Astro configuration file
├── package.json        # Project dependencies and scripts
├── tsconfig.json       # TypeScript configuration
└── README.md           # This file
```

## 🌟 Key Features Implemented

*   **Homepage (`src/pages/index.astro`):**
    *   Introduction to SOS-PC.
    *   Detailed service cards with hover effects.
    *   Interactive map showing the primary intervention zone.
    *   "Vitrine Web" section with a Swiper.js carousel to showcase past projects.
    *   Contact form (Netlify-ready) and direct contact buttons.
*   **Layout (`src/layouts/Layout.astro`):**
    *   Basic HTML structure, including global styles and theme management.
    *   Navbar and Footer components.
*   **Components:**
    *   `Navbar.astro`: Navigation bar.
    *   `ThemeSwitcher.astro`: Allows users to toggle between light and dark themes.
    *   `AsciiBackground.astro`: (If used) A dynamic ASCII art background.
    *   `CoverPage.astro`: (If used) A potential cover or landing page element.

## 🤝 Contributing

Contributions, issues, and feature requests are welcome!
As this is currently a personal or small-team project, please discuss any major changes you wish to make via an issue first.

## 📄 License

This project is currently unlicensed. (Or specify a license if one is chosen, e.g., MIT License).

---

We hope this README provides a clearer understanding of the SOS-PC website project. Let's build something great!
