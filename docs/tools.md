# Tools

## Changesets

The [Changesets](https://github.com/changesets/changesets) project is a popular implementation of changefiles, designed for use by JavaScript and TypeScript monorepos in a variety of flavors (including Lerna, Yarn and PNPM Workspaces, and others).

This tool calls changefiles _changesets_, and writes them in the form of small markdown files with YAML frontmatter. Bulk versioning and publishing with a variety of inter-project links are supported, as well as a custom GitHub Action to simplify publishing workflows.

## Rush

[Rush](https://rushjs.io) is a monorepo management tool designed for large, enterprise-scale TypeScript monorepos, and includes its own implementation of the changefiles concept (along with dedicated tools for versioning, changelog generation, alpha and hotfix releases, and etc.).
