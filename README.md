# 🌱 GardenLog — My Personal Garden

Personal website to document, monitor, and share my plant collection. Each species has its own page with a gallery of real photos, care guides, troubleshooting, and growing tips.

🔗 **[View live site →](https://garden-log-dev.vercel.app/)**

---

## ✨ Features

- **Dynamic pages** — Each plant is automatically generated from JSON data using Astro dynamic routes.
- **Photo gallery** — Real photos from the garden from multiple angles and growth stages.
- **Detailed care guides** — Watering, light, temperature, difficulty, soil, pH, and more.
- **Troubleshooting** — Common problems and solutions for each plant.
- **Responsive design** — Adapted for mobile, tablet, and desktop.
- **Accessibility** — Support for `prefers-reduced-motion` and visible focus states.

---

## 🛠️ Tech Stack

| Tool | Version | Usage |
|---|---|---|
| [Astro](https://astro.build) | 5.x | Static web framework |
| [Tailwind CSS](https://tailwindcss.com) | 4.x | Utility-first CSS (via Vite plugin) |
| [pnpm](https://pnpm.io) | — | Package manager |

---

## 📁 Project Structure

```
gardenLog/
├── public/
│   └── images/           # Real photographs of the plants (JPEG)
├── src/
│   ├── components/       # Hero, PlantCollection, Header, Footer
│   ├── data/
│   │   └── plants.json   # Data for all plants (care, tips, gallery)
│   ├── layouts/
│   │   └── Layout.astro  # Main layout
│   ├── pages/
│   │   ├── index.astro   # Main page
│   │   └── plants/
│   │       └── [slug].astro  # Dynamic route per plant
│   └── styles/
│       └── main.css      # Global styles and design system
├── astro.config.mjs
├── package.json
└── tsconfig.json
```

---

## 🚀 Quick Start

```bash
# Clone the repository
git clone https://github.com/your-username/gardenLog.git
cd gardenLog

# Install dependencies
pnpm install

# Start development server
pnpm dev
```

The site will be available at `http://localhost:4321`.

### Other commands

| Command | Action |
|---|---|
| `pnpm dev` | Development server with hot reload |
| `pnpm build` | Generates the static site in `./dist/` |
| `pnpm preview` | Previews the production build |

---

## 📝 Todo

- [ ] Add more plants
- [ ] Add live sensor telemetry following the [implementation roadmap](./ROADMAP.md)

## 🤝 Contributing

This is a personal project, but suggestions are welcome:

1. Open an **issue** describing the problem or suggestion.
2. If you want to contribute code, **fork** the repository and create a **pull request**.

---

## 📄 License

This project is for personal and educational use.

---

<p align="center">Developed with 💚 to document and monitor my plants</p>
