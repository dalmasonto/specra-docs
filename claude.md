# Specra Docs - Claude Developer Guide

## Introduction
Hello Claude! This document helps you understand specra-docs, the official documentation website for the Specra framework. This project is special because it's both the documentation for Specra AND a real-world implementation built with Specra itself.

## Project Context

### What is specra-docs?
specra-docs is a Next.js website that:
- **Documents Specra**: Provides comprehensive guides, API references, and tutorials
- **Demonstrates Specra**: Shows all features in a production environment
- **Validates Specra**: Uses the SDK it documents (dogfooding)
- **Onboards Users**: First stop for developers learning Specra

### The Circular Relationship
```
┌─────────────────┐
│   specra-sdk    │ ◄─── Built with this
│  (The Product)  │
└────────┬────────┘
         │
         │ is documented by
         ▼
┌─────────────────┐
│  specra-docs    │ ◄─── You are here
│ (Documentation) │
└────────┬────────┘
         │
         │ refers users to
         ▼
┌─────────────────┐
│  create-specra  │
│     (CLI)       │
└─────────────────┘
```

**Key Insight**: This site IS a Specra documentation site, documenting HOW to build Specra documentation sites. It's meta, but powerful - users can inspect the source code to see exactly how features work.

### Project Relationships

| Project | What It Does | How specra-docs Relates |
|---------|-------------|-------------------------|
| **specra-sdk** | Provides the framework | specra-docs uses it as dependency |
| **create-specra** | Scaffolds new projects | specra-docs documents how to use it |
| **specra-docs** | Documents everything | This project, uses SDK to document SDK |

## Architecture Overview

### Technology Stack
```
Framework Layer:
├── Next.js 16.1.0 (App Router, React Server Components)
├── React 19.2.3
└── TypeScript 5

Documentation Layer:
├── Specra 0.1.7 (the SDK itself!)
└── MDX (via Specra's MDX processor)

Styling:
├── Tailwind CSS 4.1.9
├── @tailwindcss/typography (for prose)
└── PostCSS

Optional Features:
├── MeiliSearch (search, currently disabled)
└── Analytics (Google Analytics, Plausible - ready to use)

Build & Deploy:
├── Vercel (hosting)
├── tsx (script execution)
└── ESLint (linting)
```

### Project Structure Deep Dive

```
specra-docs/
├── app/                          # Next.js App Router
│   ├── layout.tsx                # Root layout
│   │   └── Uses specra/app/layout with custom wrapper
│   ├── page.tsx                  # Landing page (8KB custom code)
│   │   └── Hero, features, CTA sections
│   ├── globals.css               # Global styles (imports specra styles)
│   ├── not-found.tsx             # Custom 404 page
│   │
│   ├── docs/                     # Documentation routes
│   │   └── [version]/
│   │       └── [...slug]/
│   │           └── page.tsx      # Re-exports specra/app/docs-page
│   │
│   └── api/                      # API routes
│       └── (potential API endpoints)
│
├── docs/                         # Documentation content (MDX)
│   └── v1.0.0/                   # Version 1.0.0 docs
│       ├── getting-started/
│       │   ├── introduction.mdx
│       │   ├── installation.mdx
│       │   └── quick-start.mdx
│       ├── configuration/
│       │   ├── overview.mdx
│       │   ├── site-config.mdx
│       │   ├── theme-config.mdx
│       │   └── navigation.mdx
│       ├── components/
│       │   ├── overview.mdx
│       │   ├── callout.mdx
│       │   ├── code-block.mdx
│       │   └── tabs.mdx
│       ├── api/
│       │   ├── configuration.mdx
│       │   ├── components.mdx
│       │   └── utilities.mdx
│       ├── guides/
│       │   ├── versioning.mdx
│       │   ├── deployment.mdx
│       │   ├── search.mdx
│       │   └── customization.mdx
│       └── examples/
│           └── use-cases.mdx
│
├── public/                       # Static assets
│   ├── icon-light-32x32.png     # Favicon (light mode)
│   ├── icon-dark-32x32.png      # Favicon (dark mode)
│   ├── logo.svg                 # Site logo
│   └── images/                  # Documentation images
│       └── screenshots/
│
├── scripts/                      # Build and utility scripts
│   ├── generate-redirects.mjs   # Generate redirect rules
│   ├── generate-static-redirects.mjs  # For static export
│   ├── index-search.ts          # Index content in MeiliSearch
│   └── test-search.ts           # Test search functionality
│
├── specra.config.json            # Specra configuration (115 lines)
├── next.config.mjs               # Next.js config (1 line - simple!)
├── tailwind.config.ts            # Tailwind configuration
├── tsconfig.json                 # TypeScript configuration
├── postcss.config.mjs            # PostCSS configuration
├── package.json                  # Dependencies and scripts
├── redirects.json                # Redirect rules
├── UPDATED_STRUCTURE.md          # Structure documentation
└── README.md                     # Project readme
```

## Key Files Explained

### 1. app/layout.tsx
```typescript
// Custom layout wrapping Specra's layout
import { ReactNode } from 'react'
import SpecraLayout from 'specra/app/layout'
import './globals.css'

export default function RootLayout({
  children,
}: {
  children: ReactNode
}) {
  return <SpecraLayout>{children}</SpecraLayout>
}

export { generateMetadata } from 'specra/app/layout'
```

**Purpose**: Wraps Specra's layout, allows for global styles and customization.

### 2. app/page.tsx (Landing Page)
```typescript
// Custom landing page (not using Specra's docs template)
import Link from 'next/link'
import { Button } from 'specra/components'

export default function Home() {
  return (
    <div className="container">
      <section className="hero">
        <h1>Specra</h1>
        <p>Modern documentation for Next.js</p>
        <Link href="/docs/v1.0.0/getting-started">
          <Button>Get Started →</Button>
        </Link>
      </section>

      <section className="features">
        {/* Feature cards */}
      </section>

      <section className="cta">
        {/* Call to action */}
      </section>
    </div>
  )
}
```

**Purpose**: Custom marketing page separate from documentation.

### 3. app/docs/[version]/[...slug]/page.tsx
```typescript
// Simply re-exports Specra's docs page
export { default } from 'specra/app/docs-page'
export {
  generateStaticParams,
  generateMetadata,
} from 'specra/app/docs-page'
```

**Purpose**: Delegates all documentation rendering to Specra. This is the magic - no custom code needed!

### 4. specra.config.json (The Brain)
```json
{
  "$schema": "./node_modules/specra/config/specra.config.schema.json",
  "site": {
    "title": "Specra Docs",
    "description": "Comprehensive documentation for your project",
    "url": "https://specra.vercel.app",
    "baseUrl": "/",
    "language": "en",
    "organizationName": "dalmasonto",
    "projectName": "specra",
    "activeVersion": "v1.0.0",
    "favicon": "/icon-light-32x32.png"
  },
  "theme": {
    "defaultMode": "system",
    "respectPrefersColorScheme": true
  },
  "navigation": {
    "showSidebar": true,
    "collapsibleSidebar": true,
    "showBreadcrumbs": true,
    "showTableOfContents": true,
    "tocPosition": "right",
    "tocMaxDepth": 3,
    "tabGroups": [
      {
        "id": "guides",
        "label": "Guides",
        "icon": "book-open"
      },
      {
        "id": "api",
        "label": "API Reference",
        "icon": "zap"
      },
      {
        "id": "components",
        "label": "Components",
        "icon": "layers"
      }
    ]
  },
  "social": {
    "github": "https://github.com/dalmasonto/specra-docs",
    "twitter": "https://twitter.com/dalmasonto",
    "discord": "https://discord.com/invite/dalmasonto"
  },
  "search": {
    "enabled": false,
    "provider": "meilisearch",
    "meilisearch": {
      "host": "http://localhost:7700",
      "apiKey": "aSampleMasterKey",
      "indexName": "docs"
    }
  },
  "footer": {
    "copyright": "Copyright © 2026 Specra. All rights reserved.",
    "links": [
      {
        "title": "Documentation",
        "items": [
          { "label": "Getting Started", "href": "/docs/v1.0.0/getting-started" },
          { "label": "API Reference", "href": "/docs/v1.0.0/api" }
        ]
      }
    ]
  }
}
```

**Purpose**: Configures every aspect of Specra's behavior.

### 5. next.config.mjs (Minimal!)
```javascript
// That's it - one line!
export { default } from 'specra/next-config'
```

**Purpose**: Uses Specra's Next.js configuration. No custom config needed!

### 6. docs/v1.0.0/getting-started/introduction.mdx (Content)
```mdx
---
title: Introduction to Specra
description: Learn what Specra is and why you should use it
tabGroup: guides
order: 1
---

# Introduction to Specra

Specra is a modern documentation framework for Next.js...

## Features

- **MDX Support** - Write in Markdown with React components
- **Versioning** - Manage multiple doc versions
- **Search** - Full-text search integration
- **Themes** - Dark and light modes

## Quick Example

import { Callout } from 'specra/components'

<Callout type="info">
  This entire site is built with Specra!
</Callout>

## Next Steps

Check out the [Installation Guide](/docs/v1.0.0/getting-started/installation).
```

**Purpose**: Actual documentation content. MDX allows mixing Markdown with React components.

## Configuration Deep Dive

### Tab Groups
```json
{
  "navigation": {
    "tabGroups": [
      {
        "id": "guides",
        "label": "Guides",
        "icon": "book-open"
      },
      {
        "id": "api",
        "label": "API Reference",
        "icon": "zap"
      },
      {
        "id": "components",
        "label": "Components",
        "icon": "layers"
      }
    ]
  }
}
```

**What This Does**:
- Creates 3 navigation tabs at the top
- Each tab can have its own sidebar structure
- Documents specify which tab via frontmatter: `tabGroup: guides`
- Helps organize large documentation into logical sections

### Social Links
```json
{
  "social": {
    "github": "https://github.com/dalmasonto/specra-docs",
    "twitter": "https://twitter.com/dalmasonto",
    "discord": "https://discord.com/invite/dalmasonto",
    "custom": [
      {
        "label": "Website",
        "url": "https://craftfolio.com/dalmasonto"
      }
    ]
  }
}
```

**What This Does**:
- Adds social icons to header/footer
- Built-in support for common platforms
- Custom links for anything else

### Footer Configuration
```json
{
  "footer": {
    "copyright": "Copyright © 2026 Specra. All rights reserved.",
    "links": [
      {
        "title": "Documentation",
        "items": [
          { "label": "Getting Started", "href": "/docs/v1.0.0/getting-started" }
        ]
      },
      {
        "title": "Community",
        "items": [
          { "label": "GitHub", "href": "https://github.com/dalmasonto/specra-docs" }
        ]
      }
    ],
    "branding": {
      "showBranding": true,
      "logo": "https://..."
    }
  }
}
```

**What This Does**:
- Creates organized footer with link columns
- Copyright notice
- Optional branding logo

## Build Scripts Explained

### 1. Development
```bash
npm run dev
# → next dev
# Starts local development server at http://localhost:3000
```

### 2. Production Build (Default - Vercel)
```bash
npm run build
# → npm run generate:redirects && NEXT_BUILD_MODE=default next build
#
# 1. Generates redirect rules
# 2. Builds with server-side rendering (SSR)
# 3. Optimized for Vercel deployment
```

### 3. Static Export (GitHub Pages)
```bash
npm run build:export
# → npm run generate:redirects &&
#    NEXT_PUBLIC_BASE_PATH=/specra-docs
#    NEXT_BUILD_MODE=export
#    next build &&
#    npm run generate:static-redirects
#
# 1. Generates redirects
# 2. Sets base path for subdirectory hosting
# 3. Exports to static HTML (out/)
# 4. Creates static redirect files
```

### 4. Search Indexing
```bash
npm run index:search
# → tsx scripts/index-search.ts
#
# 1. Scans all MDX files in docs/
# 2. Extracts content and metadata
# 3. Pushes to MeiliSearch instance
```

### 5. Search Testing
```bash
npm run test:search
# → tsx scripts/test-search.ts
#
# Tests search queries against indexed content
```

## Scripts Deep Dive

### scripts/generate-redirects.mjs
**Purpose**: Creates redirect rules for proper routing

```javascript
// Generates redirects.json
// Example: /docs → /docs/v1.0.0 (default version)
{
  "/docs": "/docs/v1.0.0",
  "/docs/": "/docs/v1.0.0/"
}
```

### scripts/generate-static-redirects.mjs
**Purpose**: Creates HTML redirect files for static hosting

```javascript
// For GitHub Pages, creates out/docs.html:
<!DOCTYPE html>
<html>
  <head>
    <meta http-equiv="refresh" content="0; url=/specra-docs/docs/v1.0.0">
  </head>
</html>
```

### scripts/index-search.ts
**Purpose**: Indexes documentation for search

```typescript
import { MeiliSearch } from 'meilisearch'
import fs from 'fs'
import path from 'path'
import matter from 'gray-matter'

const client = new MeiliSearch({
  host: 'http://localhost:7700',
  apiKey: 'aSampleMasterKey'
})

// Read all MDX files
const docsDir = path.join(process.cwd(), 'docs/v1.0.0')
const files = getAllMdxFiles(docsDir)

// Index each file
const documents = files.map(file => {
  const content = fs.readFileSync(file, 'utf-8')
  const { data, content: body } = matter(content)

  return {
    id: file,
    title: data.title,
    description: data.description,
    content: body,
    url: fileToUrl(file)
  }
})

await client.index('docs').addDocuments(documents)
```

### scripts/test-search.ts
**Purpose**: Verify search is working

```typescript
import { MeiliSearch } from 'meilisearch'

const client = new MeiliSearch({
  host: 'http://localhost:7700',
  apiKey: 'aSampleMasterKey'
})

// Test query
const results = await client.index('docs').search('components', {
  limit: 5,
  attributesToHighlight: ['title', 'content']
})

console.log(`Found ${results.hits.length} results`)
results.hits.forEach(hit => {
  console.log(`- ${hit.title}: ${hit.url}`)
})
```

## Development Workflow

### Setting Up
```bash
# Clone repository
git clone https://github.com/dalmasonto/specra-docs.git
cd specra-docs

# Install dependencies
npm install

# Start development server
npm run dev

# Open http://localhost:3000
```

### Adding Documentation

#### 1. Create MDX File
```bash
# Create new guide
touch docs/v1.0.0/guides/my-new-guide.mdx
```

#### 2. Write Content
```mdx
---
title: My New Guide
description: Learn how to do something
tabGroup: guides
order: 10
---

# My New Guide

Content goes here...

## Section 1

More content...
```

#### 3. View Changes
- Next.js hot-reloads automatically
- Navigate to `/docs/v1.0.0/guides/my-new-guide`
- Appears in sidebar automatically

### Customizing Configuration

#### 1. Edit specra.config.json
```json
{
  "theme": {
    "primaryColor": "#your-color"
  }
}
```

#### 2. Save and Reload
Changes take effect immediately in development.

### Adding Components to MDX

```mdx
---
title: Component Example
---

import { Callout, Tabs, Tab } from 'specra/components'

# Component Examples

<Callout type="warning">
  This is a warning callout!
</Callout>

<Tabs>
  <Tab title="JavaScript">
    ```js
    console.log('Hello')
    ```
  </Tab>
  <Tab title="TypeScript">
    ```ts
    console.log('Hello')
    ```
  </Tab>
</Tabs>
```

## Deployment

### Vercel (Current)
```bash
# Automatic deployment
# - Push to main branch
# - Vercel builds and deploys automatically
# - Uses npm run build
# - Live at https://specra.vercel.app
```

**Configuration**:
- Build Command: `npm run build`
- Output Directory: `.next`
- Install Command: `npm install`

### GitHub Pages (Alternative)
```bash
# Build static site
npm run build:export

# Output in out/
ls out/

# Deploy out/ to gh-pages branch
npm install -g gh-pages
gh-pages -d out
```

**Configuration Needed**:
```json
// specra.config.json
{
  "deployment": {
    "target": "github",
    "basePath": "/specra-docs",  // or "" if custom domain
    "customDomain": false         // or true if custom domain
  }
}
```

### Netlify
```bash
# Build: npm run build
# Publish: .next
# Environment: Node 18+
```

## Search Integration (When Enabled)

### 1. Setup MeiliSearch
```bash
# Install MeiliSearch
curl -L https://install.meilisearch.com | sh

# Run
./meilisearch --master-key="aSampleMasterKey"
```

### 2. Enable in Config
```json
{
  "search": {
    "enabled": true,
    "provider": "meilisearch",
    "meilisearch": {
      "host": "http://localhost:7700",
      "apiKey": "aSampleMasterKey",
      "indexName": "docs"
    }
  }
}
```

### 3. Index Content
```bash
npm run index:search
```

### 4. Test
```bash
npm run test:search
# Or use the search UI in the browser
```

## Common Tasks

### Add New Documentation Page
1. Create MDX file in appropriate directory
2. Add frontmatter (title, description, tabGroup)
3. Write content
4. File automatically appears in navigation

### Change Theme Colors
```json
// specra.config.json
{
  "theme": {
    "primaryColor": "#0070f3",
    "defaultMode": "dark"  // or "light" or "system"
  }
}
```

### Add New Tab Group
```json
{
  "navigation": {
    "tabGroups": [
      // ... existing groups
      {
        "id": "tutorials",
        "label": "Tutorials",
        "icon": "graduation-cap"
      }
    ]
  }
}
```

Then in MDX:
```mdx
---
tabGroup: tutorials
---
```

### Update Social Links
```json
{
  "social": {
    "github": "your-github-url",
    "twitter": "your-twitter",
    // Add more platforms
  }
}
```

### Customize Footer
```json
{
  "footer": {
    "copyright": "Your copyright text",
    "links": [
      // Your link columns
    ]
  }
}
```

## Troubleshooting

### Styles Not Loading
- Check `app/globals.css` imports `specra/styles`
- Verify Tailwind configuration
- Clear `.next` cache: `rm -rf .next && npm run dev`

### Documentation Not Appearing
- Check MDX file location (must be in `docs/v1.0.0/`)
- Verify frontmatter is valid YAML
- Check console for errors

### Search Not Working
- Ensure MeiliSearch is running
- Verify `search.enabled: true`
- Run `npm run index:search`
- Check API key and host in config

### Build Fails
- Check for syntax errors in MDX files
- Verify all imports are valid
- Check Next.js version compatibility
- Clear node_modules and reinstall

## Best Practices

### For Documentation Content
1. **Clear Frontmatter**: Always include title and description
2. **Logical Organization**: Use tab groups effectively
3. **Code Examples**: Provide working examples
4. **Cross-Links**: Link related pages
5. **Images**: Use relative paths in public/

### For Configuration
1. **Version Control**: Commit specra.config.json changes
2. **Comments**: Document custom settings
3. **Validation**: Use schema for auto-completion
4. **Testing**: Test in development before deploying

### For Maintenance
1. **Keep Updated**: Regular specra SDK updates
2. **Monitor**: Check for broken links
3. **Search**: Re-index after major content changes
4. **Performance**: Monitor build times

## Why This Project Matters

### For Specra Users
- **Living Example**: See Specra in action
- **Source of Truth**: Official documentation
- **Learning Resource**: Study real implementation
- **Inspiration**: See what's possible

### For Specra Development
- **Dogfooding**: Catches SDK bugs early
- **Feature Validation**: Proves features work
- **User Testing**: Real-world usage patterns
- **Quality Assurance**: High-stakes use case

## Resources

### Official
- **Live Site**: https://specra.vercel.app
- **Repository**: https://github.com/dalmasonto/specra-docs

### Related
- **SDK**: https://github.com/dalmasonto/specra
- **CLI**: https://github.com/dalmasonto/specra-cli

### Tools
- **Next.js**: https://nextjs.org
- **Vercel**: https://vercel.com
- **MeiliSearch**: https://meilisearch.com

## Contact

**Authors**: dalmasonto, arthur-kamau
**License**: (Check repository for license)

---

This guide should help you understand, maintain, and extend specra-docs. Remember: this site is both documentation AND demonstration - any changes showcase Specra's capabilities!
