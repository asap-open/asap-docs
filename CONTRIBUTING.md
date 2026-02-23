# Contributing to ASAP Docs

Thank you for helping improve the ASAP documentation. Accurate, clear docs make a real difference for everyone using or building on the platform.

## Tech Stack

- **VitePress** v2 for the static site
- **Vue 3** for custom components (if needed)
- Markdown for all content
- Custom CSS theme in `.vitepress/theme/custom.css`

## Prerequisites

- Node.js 20+
- Yarn

## Local Setup

```bash
# 1. Fork and clone the repo
git clone https://github.com/asap-open/asap-docs.git
cd asap-docs

# 2. Install dependencies
yarn install

# 3. Start the dev server
yarn docs:dev
```

The docs site will be available at `http://localhost:5173`.

## Available Scripts

| Command             | Description                                 |
| ------------------- | ------------------------------------------- |
| `yarn docs:dev`     | Start VitePress dev server with hot reload  |
| `yarn docs:build`   | Build the static site to `.vitepress/dist/` |
| `yarn docs:preview` | Preview the production build locally        |

## Project Structure

```
docs/
  index.md              # Home page
  installation.md       # Self-hosting guide
  .vitepress/
    config.mts          # Site config, nav, sidebar
    theme/
      index.ts          # Theme entry
      custom.css        # Custom CSS (color palette, overrides)
  guide/
    index.md            # Introduction
    quick-start.md      # Quick start walkthrough
    features/           # Feature guides (sessions, exercises, progress, profile)
  api/
    authentication.md
    sessions.md
    exercises.md
    weights.md
    progress.md
    profile.md
  public/               # Static assets (logo, etc.)
```

## Contribution Guidelines

### What to Work On

- **Fixing inaccuracies** — if an API field name, endpoint path, or behavior described in the docs doesn't match the actual server code, please fix it
- **Improving clarity** — reword confusing sections, add examples, or expand sparse explanations
- **Adding missing content** — new endpoints, features, or configuration options not yet documented
- **Fixing typos and formatting** — always welcome

### Writing Style

- Write in plain, direct English
- Use second person ("you") when addressing the reader
- Prefer short sentences and paragraphs
- Use code blocks for all commands, request/response examples, and config snippets
- Use tables for parameter references and option lists

### API Documentation Format

Each API doc page should follow this structure per endpoint:

````markdown
## Endpoint Name

`METHOD /path`

Brief description.

**Authentication:** Required / Not required

**Request body / Query params** (if any)

```json
{ "field": "value" }
```

**Response**

```json
{ "field": "value" }
```
````

Always verify field names against the actual server controller code before documenting them.

### Sidebar and Navigation

If you add a new page, register it in `.vitepress/config.mts` under the appropriate sidebar section. Follow the existing structure — do not create top-level nav items without discussion.

### Branching

```
fix/sessions-api-field-names
docs/add-progress-endpoint-examples
improve/installation-clarity
```

### Commits

```
fix: correct sessionName field in sessions API docs
docs: add progress consistency endpoint documentation
style: improve readability of quick-start guide
```

### Pull Requests

1. Fork the repository
2. Create a branch off `main`
3. Make your changes
4. Run `yarn docs:build` locally to catch any broken links or build errors
5. Open a PR with a brief description of what you changed and why

## Deployment

The docs site is automatically built and deployed to GitHub Pages on every push to `main` via the GitHub Actions workflow at `.github/workflows/deploy.yml`. You do not need to manually deploy.

## Questions

Open an issue if you're unsure whether a content change is appropriate or want to propose a larger restructure before investing time in it.
