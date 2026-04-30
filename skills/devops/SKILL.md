---
name: devops
description: Use this skill when the user asks about Docker, containerization, CI/CD pipelines, GitHub Actions, deployment configurations, hosting setup, environment management, build optimization, CDN configuration, or infrastructure as code. Also triggers for "deploy this", "set up CI", "create a Dockerfile", "configure Vercel/Netlify/Cloudflare", "optimize build times", or "set up preview deployments". Covers frontend deployment workflows including static site hosting, SSR/edge deployment, image optimization pipelines, and performance budgets.
license: Complete terms in LICENSE.txt
---

# DevOps

You are helping with infrastructure, deployment, and CI/CD. Follow these patterns.

## Docker

### Frontend App Dockerfile (Multi-stage)

```dockerfile
# Stage 1: Dependencies
FROM node:22-alpine AS deps
WORKDIR /app
COPY package.json pnpm-lock.yaml ./
RUN corepack enable && pnpm install --frozen-lockfile

# Stage 2: Build
FROM node:22-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN corepack enable && pnpm build

# Stage 3: Production
FROM node:22-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production

RUN addgroup --system --gid 1001 appgroup
RUN adduser --system --uid 1001 appuser

COPY --from=builder /app/public ./public
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static

USER appuser
EXPOSE 3000
ENV PORT=3000
CMD ["node", "server.js"]
```

### Docker Compose for Local Dev

```yaml
# docker-compose.yml
services:
  app:
    build:
      context: .
      target: deps
    command: pnpm dev
    ports:
      - "3000:3000"
    volumes:
      - .:/app
      - /app/node_modules
    environment:
      - NODE_ENV=development

  db:
    image: postgres:16-alpine
    ports:
      - "5432:5432"
    environment:
      POSTGRES_DB: app
      POSTGRES_USER: dev
      POSTGRES_PASSWORD: dev
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

### Rules

- Always use multi-stage builds to minimize image size
- Never run as root in production containers
- Pin base image versions (not `latest`)
- Use `.dockerignore` to exclude `node_modules`, `.next`, `.git`
- Set `NODE_ENV=production` in the final stage

## GitHub Actions

### Full CI/CD Pipeline

```yaml
name: CI/CD
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  quality:
    name: Code Quality
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: pnpm

      - run: pnpm install --frozen-lockfile
      - run: pnpm lint
      - run: pnpm type-check
      - run: pnpm test --run --coverage

      - name: Upload coverage
        uses: codecov/codecov-action@v4
        with:
          files: coverage/lcov.info

  build:
    name: Build
    needs: quality
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: pnpm

      - run: pnpm install --frozen-lockfile
      - run: pnpm build

      - name: Check bundle size
        uses: andresz1/size-limit-action@v1
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}

  e2e:
    name: E2E Tests
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: pnpm

      - run: pnpm install --frozen-lockfile
      - run: pnpm exec playwright install --with-deps chromium

      - run: pnpm build
      - run: pnpm exec playwright test

      - uses: actions/upload-artifact@v4
        if: failure()
        with:
          name: playwright-report
          path: playwright-report/

  deploy:
    name: Deploy
    needs: [build, e2e]
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    runs-on: ubuntu-latest
    environment: production
    steps:
      - uses: actions/checkout@v4
      # Add your deployment step here (Vercel, Cloudflare, etc.)
```

### Preview Deployments

```yaml
# .github/workflows/preview.yml
name: Preview
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  deploy-preview:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Deploy Preview
        uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}

      - name: Comment PR
        uses: actions/github-script@v7
        with:
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: `🚀 Preview: ${process.env.PREVIEW_URL}`
            })
```

## Deployment Platforms

### Vercel (Recommended for Next.js)

```json
// vercel.json
{
  "framework": "nextjs",
  "buildCommand": "pnpm build",
  "installCommand": "pnpm install",
  "headers": [
    {
      "source": "/fonts/(.*)",
      "headers": [
        {
          "key": "Cache-Control",
          "value": "public, max-age=31536000, immutable"
        }
      ]
    },
    {
      "source": "/(.*)",
      "headers": [
        { "key": "X-Content-Type-Options", "value": "nosniff" },
        { "key": "X-Frame-Options", "value": "DENY" },
        { "key": "Referrer-Policy", "value": "strict-origin-when-cross-origin" }
      ]
    }
  ]
}
```

### Cloudflare Pages

```toml
# wrangler.toml
name = "my-app"
compatibility_date = "2026-01-01"
pages_build_output_dir = "out"

[build]
command = "pnpm build"
```

## Performance Budgets

```javascript
// size-limit.config.js
module.exports = [
  { path: ".next/static/chunks/*.js", limit: "200 KB", gzip: true },
  { path: ".next/static/css/*.css", limit: "30 KB", gzip: true },
  { path: "public/images/*", limit: "100 KB" },
];
```

### Lighthouse CI

```yaml
# lighthouserc.json
{
  "ci":
    {
      "collect":
        {
          "url": ["http://localhost:3000/", "http://localhost:3000/about"],
          "startServerCommand": "pnpm start",
          "numberOfRuns": 3,
        },
      "assert":
        {
          "assertions":
            {
              "categories:performance": ["error", { "minScore": 0.9 }],
              "categories:accessibility": ["error", { "minScore": 0.95 }],
              "categories:best-practices": ["error", { "minScore": 0.9 }],
              "categories:seo": ["warn", { "minScore": 0.9 }],
            },
        },
    },
}
```

## Environment Management

```bash
# Use .env files by environment
.env                 # Shared defaults (committed)
.env.local           # Local overrides (gitignored)
.env.development     # Dev-specific
.env.production      # Prod-specific
.env.test            # Test-specific
```

### Secrets: Never commit. Use:

- GitHub Actions secrets for CI/CD
- Vercel/platform environment variables for deployment
- `.env.local` for local development (always gitignored)

## Rules

- Always use `--frozen-lockfile` in CI to prevent lock file drift
- Use `concurrency` in GitHub Actions to cancel stale runs
- Set up Dependabot or Renovate for automated dependency updates
- Cache `node_modules` and build artifacts between CI steps
- Run E2E tests against a production build, not dev server
- Add security headers to all deployment configs
- Set `immutable` cache headers for hashed static assets
