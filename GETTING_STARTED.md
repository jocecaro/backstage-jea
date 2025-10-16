# Getting Started with Backstage JEA

Welcome! This guide will help you get started with your new Backstage instance.

## 🎯 What's Included

This Backstage setup includes:

- ✅ **Core Backstage** - Software catalog, TechDocs, scaffolder, and more
- ✅ **Docker Support** - Run locally with Docker Compose or deploy to production
- ✅ **Kubernetes Manifests** - Ready for AWS EKS deployment
- ✅ **AWS GenAI Plugin** - AI assistant powered by AWS Bedrock or OpenAI
- ✅ **AWS Cost Insights** - Monitor and optimize AWS infrastructure costs

## 🚀 Quick Start (5 minutes)

### Option 1: Run Locally (No Docker)

Best for development and customization:

```bash
# 1. Install dependencies
yarn install

# 2. Start Backstage (builds automatically in development mode)
yarn start
```

This starts both the frontend (port 3000) and backend (port 7007).
Access at: http://localhost:3000

**Note**: `yarn start` automatically builds and hot-reloads during development. No separate build step is needed.

### Option 2: Run with Docker

Best for testing production setup:

```bash
# 1. Build the application
yarn install
yarn tsc
yarn build:backend  # Builds both frontend and backend

# 2. Start with Docker Compose
docker-compose up -d

# 3. View logs (optional)
docker-compose logs -f backstage
```

Access at: http://localhost:7007

**Note**: The backend build includes the frontend as a static bundle served by the backend.

## 📖 Next Steps

### 1. Explore the Interface

- **Catalog**: Browse components, APIs, and resources
- **Create**: Use software templates to scaffold new projects
- **Docs**: Read TechDocs for components
- **AI Assistant**: Ask questions about your platform (requires AWS/OpenAI setup)
- **Cost Insights**: View AWS spending (requires AWS Cost Explorer access)

### 2. Add Your First Component

Create a file `catalog-info.yaml` in your project:

```yaml
apiVersion: backstage.io/v1alpha1
kind: Component
metadata:
  name: my-awesome-service
  description: My first Backstage component
  annotations:
    github.com/project-slug: org/repo
spec:
  type: service
  lifecycle: production
  owner: team-a
```

Then add it to the catalog:

1. Navigate to "Create" → "Register Existing Component"
2. Enter the URL to your `catalog-info.yaml`
3. Click "Analyze" and "Import"

### 3. Configure GitHub Integration (Optional but Recommended)

1. **Create a GitHub Personal Access Token**

   - Go to GitHub Settings → Developer settings → Personal access tokens
   - Generate a token with `repo` and `read:org` scopes
   - Copy the token

2. **Add to Configuration**

   ```bash
   # For local development
   export GITHUB_TOKEN=your_token_here
   yarn start
   ```

   Or add to `.env` file:

   ```
   GITHUB_TOKEN=your_token_here
   ```

### 4. Set Up AWS Plugins (Optional)

See [AWS Plugins Documentation](./docs/AWS_PLUGINS.md) for:

- Configuring AWS Bedrock for the AI Assistant
- Setting up AWS Cost Explorer for cost tracking
- IAM permissions required

## 📚 Documentation

| Document                                    | Description                               |
| ------------------------------------------- | ----------------------------------------- |
| [README.md](./README.md)                    | Project overview and quick reference      |
| [AWS Plugins Guide](./docs/AWS_PLUGINS.md)  | Configure GenAI and Cost Insights plugins |
| [Docker Guide](./docs/DOCKER_GUIDE.md)      | Docker deployment and troubleshooting     |
| [Kubernetes Guide](./k8s/README.md)         | Deploy to AWS EKS                         |
| [Backstage Docs](https://backstage.io/docs) | Official Backstage documentation          |

## 🔧 Common Tasks

### Update Dependencies

```bash
yarn install
```

### Build for Production

```bash
yarn install
yarn tsc
yarn build:backend  # Includes building the frontend as a dependency
```

**Note**: The `yarn build:backend` command automatically builds the frontend app first, then builds the backend with the frontend bundle included. This produces a single deployable backend package.

### Run Tests

```bash
yarn test
```

### Format Code

```bash
yarn prettier --write .
```

### Lint Code

```bash
yarn lint:all
```

## 🐛 Troubleshooting

### Port Already in Use

```bash
# Check what's using port 3000 or 7007
lsof -i :3000
lsof -i :7007

# Kill the process or change ports in app-config.yaml
```

### Dependencies Won't Install

```bash
# Clear cache and reinstall
rm -rf node_modules yarn.lock
yarn cache clean
yarn install
```

### Database Errors

If using Docker:

```bash
# Restart PostgreSQL
docker-compose restart postgres

# View PostgreSQL logs
docker-compose logs postgres
```

### AWS Plugin Errors

See [AWS Plugins Troubleshooting](./docs/AWS_PLUGINS.md#troubleshooting)

## 🚢 Deploying to Production

### AWS ECS

1. Build and push Docker image to ECR
2. Create ECS task definition
3. Create ECS service
4. Configure load balancer

See [README - AWS ECS Section](./README.md#deploying-to-aws-ecs)

### AWS EKS

1. Apply Kubernetes manifests
2. Configure secrets
3. Set up ingress

See [Kubernetes Guide](./k8s/README.md)

## 🆘 Getting Help

- **Backstage Discord**: https://discord.gg/backstage-687207715902193673
- **GitHub Issues**: https://github.com/backstage/backstage/issues
- **AWS Plugins Issues**: https://github.com/awslabs/backstage-plugins-for-aws/issues

## 🤝 Contributing

1. Create a feature branch
2. Make your changes
3. Test locally: `yarn test && yarn lint`
4. Submit a pull request

## 📝 License

Based on [Backstage](https://backstage.io), licensed under Apache 2.0.
