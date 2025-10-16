# Kubernetes Deployment for Backstage

This directory contains Kubernetes manifests for deploying Backstage to AWS EKS.

## Prerequisites

- AWS CLI configured
- kubectl installed and configured
- eksctl (optional, for EKS cluster creation)
- An existing EKS cluster or create one:
  ```bash
  eksctl create cluster --name backstage-cluster --region us-east-1
  ```

## Deployment Steps

1. **Update the secrets**

   Edit `postgres-secret.yaml` and `backstage-secret.yaml` to set proper passwords and tokens:

   ```bash
   # For production, use AWS Secrets Manager or Sealed Secrets
   kubectl create secret generic backstage-secrets \
     --from-literal=GITHUB_TOKEN=your-token \
     --from-literal=POSTGRES_PASSWORD=strong-password \
     -n backstage
   ```

2. **Update the Backstage image**

   Edit `backstage-deployment.yaml` and replace `backstage:latest` with your ECR image:

   ```yaml
   image: <account-id>.dkr.ecr.us-east-1.amazonaws.com/backstage:latest
   ```

3. **Apply the manifests**

   ```bash
   # Create namespace
   kubectl apply -f namespace.yaml

   # Create secrets (or apply the YAML files after editing)
   kubectl apply -f postgres-secret.yaml
   kubectl apply -f backstage-secret.yaml

   # Deploy PostgreSQL
   kubectl apply -f postgres-storage.yaml
   kubectl apply -f postgres-deployment.yaml

   # Wait for PostgreSQL to be ready
   kubectl wait --for=condition=ready pod -l app=postgres -n backstage --timeout=300s

   # Deploy Backstage
   kubectl apply -f backstage-deployment.yaml
   ```

4. **Check the status**

   ```bash
   kubectl get all -n backstage
   kubectl logs -f deployment/backstage -n backstage
   ```

5. **Access the application**
   ```bash
   # Get the LoadBalancer URL
   kubectl get svc backstage -n backstage
   ```

## Using AWS ALB Ingress Controller (Recommended)

Instead of using a LoadBalancer service, you can use ALB Ingress:

1. **Install AWS Load Balancer Controller**

   ```bash
   # Follow instructions at:
   # https://docs.aws.amazon.com/eks/latest/userguide/aws-load-balancer-controller.html
   ```

2. **Create an Ingress resource** (see `ingress.yaml` for example)

## Production Considerations

1. **Database**

   - Consider using Amazon RDS for PostgreSQL instead of in-cluster PostgreSQL
   - Update connection settings in `backstage-secret.yaml`

2. **Secrets Management**

   - Use AWS Secrets Manager with External Secrets Operator
   - Or use Sealed Secrets for GitOps workflows

3. **High Availability**

   - Increase replicas in `backstage-deployment.yaml`
   - Configure HPA (Horizontal Pod Autoscaler)
   - Use Pod Disruption Budgets

4. **Storage**

   - Adjust PVC size in `postgres-storage.yaml` based on needs
   - Consider using EBS CSI driver for better performance

5. **Monitoring**
   - Set up Prometheus and Grafana
   - Configure CloudWatch Container Insights
   - Add custom metrics and alerts

## Clean Up

```bash
kubectl delete namespace backstage
```
