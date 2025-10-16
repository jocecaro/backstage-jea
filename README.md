# Backstage JEA

A customized [Backstage](https://backstage.io) instance for JEA with Docker support and AWS plugin integrations.

## 🚀 Quick Start

### Prerequisites

- Node.js 20 or 22
- Yarn 4.x (included via `.yarn/releases`)
- Docker and Docker Compose (for containerized deployment)
- Git

### Local Development (Without Docker)

1. **Clone the repository**
   ```bash
   git clone https://github.com/jocecaro/backstage-jea.git
   cd backstage-jea
   ```

2. **Install dependencies**
   ```bash
   yarn install
   ```

3. **Start the application**
   ```bash
   yarn start
   ```

   This will start both the frontend (http://localhost:3000) and backend (http://localhost:7007).

### Local Development (With Docker)

1. **Build and start services**
   ```bash
   # First build the backend
   yarn install
   yarn tsc
   yarn build:backend
   
   # Then start with Docker Compose
   docker-compose up -d
   ```

2. **Access the application**
   - Frontend & Backend: http://localhost:7007

3. **View logs**
   ```bash
   docker-compose logs -f backstage
   ```

4. **Stop services**
   ```bash
   docker-compose down
   ```

## 📦 Building for Production

### Build the Docker Image

```bash
# Install dependencies and build
yarn install
yarn tsc
yarn build:backend

# Build the Docker image
yarn build-image
```

Or using Docker Compose:

```bash
docker-compose build
```

## ☁️ AWS Deployment

### Deploying to AWS ECS

1. **Push the Docker image to Amazon ECR**
   ```bash
   # Authenticate Docker to ECR
   aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <account-id>.dkr.ecr.us-east-1.amazonaws.com
   
   # Tag and push the image
   docker tag backstage:latest <account-id>.dkr.ecr.us-east-1.amazonaws.com/backstage:latest
   docker push <account-id>.dkr.ecr.us-east-1.amazonaws.com/backstage:latest
   ```

2. **Configure ECS Task Definition**
   - Set environment variables (POSTGRES_HOST, POSTGRES_USER, etc.)
   - Configure task resources (CPU, memory)
   - Set up load balancer for port 7007

3. **Create ECS Service**
   - Use the task definition
   - Configure auto-scaling
   - Set up health checks

### Deploying to AWS EKS

1. **Create Kubernetes manifests**
   ```bash
   # Example deployment.yaml and service.yaml needed
   ```

2. **Apply to cluster**
   ```bash
   kubectl apply -f k8s/
   ```

3. **Configure ingress**
   - Set up ALB Ingress Controller
   - Configure SSL/TLS certificates

## 🔌 AWS Plugins

This Backstage instance includes AWS plugins from [backstage-plugins-for-aws](https://github.com/awslabs/backstage-plugins-for-aws):

### Installed Plugins

- **AWS Gen-AI Plugin**: AI-powered assistant using AWS Bedrock (Claude models) or OpenAI
  - Navigate to "AI Assistant" in the sidebar
  - Ask questions about your platform, infrastructure, and documentation
  
- **AWS Cost Insights Plugin**: AWS cost monitoring and optimization
  - View overall AWS spending in "Cost Insights" 
  - See component-specific costs in entity pages

For detailed configuration and usage instructions, see [AWS Plugins Documentation](./docs/AWS_PLUGINS.md).

## 🛠️ Configuration

### Environment Variables

Copy `.env.example` to `.env` and configure:

```bash
cp .env.example .env
```

Key configuration options:
- `GITHUB_TOKEN`: For GitHub integrations
- `POSTGRES_*`: Database connection settings
- `BACKEND_SECRET`: Backend authentication secret

### App Configuration

Main configuration files:
- `app-config.yaml`: Development configuration
- `app-config.production.yaml`: Production configuration
- `app-config.local.yaml`: Local overrides (not committed)

## 📚 Additional Documentation

- [Backstage Documentation](https://backstage.io/docs)
- [AWS Backstage Plugins](https://github.com/awslabs/backstage-plugins-for-aws)
- [Docker Deployment Guide](https://backstage.io/docs/deployment/docker)
- [Kubernetes Deployment Guide](https://backstage.io/docs/deployment/k8s)

## 🤝 Contributing

1. Create a feature branch
2. Make your changes
3. Test locally with `yarn test` and `yarn lint`
4. Submit a pull request

## 📄 License

This project is based on Backstage, which is licensed under the Apache License 2.0.
