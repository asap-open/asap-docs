<p align="center">
  <img src="public/logo-2.webp" alt="ASAP" width="96" />
</p>

<h1 align="center">ASAP — Documentation</h1>

<p align="center">
  VitePress documentation site for the <strong>Applied Strength & Advancement Platform</strong>.
  Deployed to GitHub Pages at <a href="https://asap-open.github.io/asap-docs/">asap-open.github.io/asap-docs</a>.
</p>

## 📚 Documentation Structure

```
docs/
├── index.md                          # Homepage with hero section
├── installation.md                   # Installation guide (Docker, Docker Compose, Manual)
├── guide/
│   ├── index.md                      # Introduction
│   ├── quick-start.md                # Getting started guide
│   └── features/
│       ├── sessions.md               # Workout sessions feature
│       ├── exercises.md              # Exercise library feature
│       ├── progress.md               # Progress tracking feature
│       └── profile.md                # Profile & stats feature
└── api/
    ├── authentication.md             # Auth API endpoints
    ├── sessions.md                   # Sessions API endpoints
    ├── exercises.md                  # Exercises API endpoints
    ├── profile.md                    # Profile API endpoints
    └── weights.md                    # Weights API endpoints
```

## 🚀 Running the Documentation

### Development

```bash
cd docs
yarn install
yarn docs:dev
```

Documentation will be available at: `http://localhost:5173`

### Build for Production

```bash
yarn docs:build
```

### Preview Production Build

```bash
yarn docs:preview
```

## 🎨 Theme Customization

The documentation uses a custom VitePress theme matching the ASAP app design:

- **Primary Color**: `#13ecd6` (cyan/teal)
- **Font**: Epilogue
- **Logo**: `/public/logo-2.webp`

Theme files:

- `docs/.vitepress/theme/custom.css` - Custom styles
- `docs/.vitepress/theme/index.ts` - Theme entry point
- `docs/.vitepress/config.mts` - VitePress configuration

## 📄 Content Overview

### Getting Started

- **Introduction** - What is ASAP and why use it
- **Installation** - Docker Compose (recommended), Docker, and manual setup
- **Quick Start** - Step-by-step guide for new users

### Features

- **Workout Sessions** - Creating and logging workouts
- **Exercise Library** - Browsing and using exercises
- **Progress Tracking** - Analytics and charts
- **Profile & Stats** - Account management and metrics

### API Reference

- **Authentication** - Sign up, sign in, logout
- **Sessions** - Create and manage workout sessions
- **Exercises** - Browse exercise library
- **Profile** - User profile management
- **Weights** - Body weight tracking

## ✅ What's Included

✅ Custom theme with app's branding (#13ecd6)
✅ Homepage with features and quick start
✅ Complete installation guide (3 methods)
✅ Introduction and quick start guide
✅ 4 feature guides with detailed instructions
✅ 5 API documentation pages
✅ Navigation and sidebar structure
✅ Logo integration
✅ Responsive design
✅ Search functionality (built-in)

## 🔧 Customization

### Update GitHub Link

Edit `docs/.vitepress/config.mts`:

```typescript
socialLinks: [{ icon: "github", link: "https://github.com/yourusername/asap" }];
```

### Add More Pages

1. Create a new `.md` file in appropriate directory
2. Add to sidebar in `config.mts`:

```typescript
sidebar: [
  {
    text: "Section Name",
    items: [{ text: "Page Title", link: "/path/to/page" }],
  },
];
```

### Modify Theme Colors

Edit `docs/.vitepress/theme/custom.css`:

```css
:root {
  --vp-c-brand-1: #13ecd6; /* Primary color */
  --vp-button-brand-bg: #13ecd6;
  /* ... */
}
```

## 📝 Writing Guide

### Code Blocks

\`\`\`bash
docker-compose up -d
\`\`\`

### Info Boxes

```markdown
::: tip
This is a helpful tip
:::

::: warning
This is a warning
:::

::: danger
This is dangerous
:::
```

### Links

```markdown
[Internal Link](/guide/quick-start)
[External Link](https://example.com)
```

## 🌐 Deployment

### GitHub Pages

Deployment is handled automatically via the GitHub Actions workflow on every push to `main`. See [`.github/workflows/deploy.yml`](.github/workflows/deploy.yml).

### Vercel

1. Connect GitHub repo
2. Set build command: `yarn docs:build`
3. Set output directory: `.vitepress/dist`

### Netlify

1. Connect GitHub repo
2. Set build command: `yarn docs:build`
3. Set publish directory: `.vitepress/dist`

## Resources

- [VitePress Documentation](https://vitepress.dev/)
- [VitePress Theme Config](https://vitepress.dev/reference/default-theme-config)
- [ASAP Live Docs](https://asap-open.github.io/asap-docs/)

## Contributing

See the [CONTRIBUTING](CONTRIBUTING) for contribution guidelines.

## License

This project is licensed under the **GPLv3 License** — see [LICENSE](LICENSE) for details.

---

Built with [VitePress](https://vitepress.dev/) • Themed to match ASAP design system
