# GitHub Copilot Instructions for Backstage JEA

## Technology Stack

This is a [Backstage](https://backstage.io) application customized for JEA with:

- **Node.js**: Version 20 or 22
- **TypeScript**: ~5.8.0
- **React**: ^18.0.2 with JSX transform (`jsx: "react-jsx"`)
- **Yarn**: 4.4.1 (workspace-based monorepo)
- **Testing**: Jest for unit tests, Playwright for E2E tests
- **Linting**: ESLint via Backstage CLI
- **Formatting**: Prettier (configured via `@backstage/cli/config/prettier`)

## Project Structure

This is a Yarn workspaces monorepo with:

- `packages/app/`: Frontend React application
- `packages/backend/`: Backend Node.js service
- `plugins/`: Custom Backstage plugins
- `docs/`: Documentation files
- `k8s/`: Kubernetes manifests
- `examples/`: Example configurations

## Code Style and Linting

- Follow the Backstage CLI linting and formatting rules
- Use the existing ESLint configuration (`.eslintrc.js`)
- Format code using Prettier before committing
- Use TypeScript for all new code
- Use React 18 features and the modern JSX transform (no need to import React)
- Follow Material-UI v4 patterns for UI components
- Run `yarn lint` to check code style
- Run `yarn fix` to automatically fix linting issues
- Run `yarn prettier:check` to verify formatting

## Testing Guidelines

- Write unit tests using Jest for new components and utilities
- Follow existing test patterns in `packages/app/src/App.test.tsx`
- Use `@testing-library/react` for React component tests
- Use `@testing-library/jest-dom` for DOM matchers
- Use Playwright for end-to-end tests (see `packages/app/e2e-tests/`)
- Run `yarn test` for unit tests
- Run `yarn test:all` for tests with coverage
- Run `yarn test:e2e` for Playwright E2E tests
- Minimum test coverage target: reasonable coverage for critical paths

## Backstage Conventions

- Use the Backstage plugin system for adding new features
- Follow Backstage naming conventions for components and APIs
- Use `@backstage/core-plugin-api` for plugin development
- Use `@backstage/catalog-model` for catalog entities
- Follow Backstage configuration patterns in `app-config.yaml`
- Use the Backstage CLI for scaffolding: `yarn new`

## AWS Plugin Integration

This project integrates AWS-specific Backstage plugins:

- **AWS Gen-AI Plugin**: Uses AWS Bedrock (Claude models) or OpenAI for AI assistance
- **AWS Cost Insights Plugin**: Monitors and optimizes AWS costs
- Use the existing AWS plugin patterns when adding new AWS-related features
- Reference `docs/AWS_PLUGINS.md` for AWS plugin documentation

## Security Best Practices

- Never commit secrets, API keys, or credentials to the repository
- Use environment variables for sensitive configuration (see `.env.example`)
- Store secrets in `BACKEND_SECRET`, `GITHUB_TOKEN`, etc.
- Follow AWS security best practices for plugin integrations
- Validate all external input
- Use secure cookie settings in production
- Follow Backstage security guidelines for authentication and authorization

## Docker and Deployment

- Docker build context is the repository root (see `packages/backend/Dockerfile`)
- Use `yarn build:backend` before building Docker images
- Follow the Docker patterns in `docker-compose.yml`
- Reference `docs/DOCKER_GUIDE.md` for Docker deployment guidance
- Kubernetes manifests are in the `k8s/` directory
- Production configuration is in `app-config.production.yaml`

## Dependencies

- Use Yarn 4.x for package management
- Add new dependencies using `yarn add <package>` in the appropriate workspace
- Follow Backstage version compatibility guidelines
- Prefer Backstage-provided packages over third-party alternatives
- Check `resolutions` in `package.json` for required dependency versions
- Run `yarn install` after adding dependencies

## Documentation

- Update relevant documentation in `docs/` when adding features
- Follow the existing markdown style in README.md
- Include code examples in documentation where helpful
- Update `GETTING_STARTED.md` for setup changes
- Document new environment variables in `.env.example`

## Git Workflow

- Create feature branches for new work
- Write clear, descriptive commit messages
- Test locally before committing: `yarn test && yarn lint`
- Follow conventional commit style where appropriate
- Keep changes focused and atomic

## Build and Start Commands

- `yarn install`: Install dependencies
- `yarn start`: Start both frontend and backend in development mode
- `yarn build:backend`: Build the backend for production
- `yarn build:all`: Build all packages
- `yarn build-image`: Build Docker image
- `yarn tsc`: Run TypeScript compiler
- `yarn clean`: Clean build artifacts

## Common Patterns

- Use `@backstage/core-components` for common UI components
- Use `@backstage/core-app-api` for app-level APIs
- Use `@backstage/catalog-react` for catalog-related components
- Follow Material-UI theming via `@backstage/theme`
- Use React Router v6 for navigation
- Use Backstage's utility functions and hooks when available

## Additional Resources

- Main Backstage documentation: https://backstage.io/docs
- AWS Backstage plugins: https://github.com/awslabs/backstage-plugins-for-aws
- Project documentation: See `docs/` directory
- Getting started guide: See `GETTING_STARTED.md`
