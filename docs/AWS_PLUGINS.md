# AWS Plugins for Backstage

This Backstage instance includes two AWS plugins from the [backstage-plugins-for-aws](https://github.com/awslabs/backstage-plugins-for-aws) project:

1. **AWS GenAI Plugin** - AI assistant powered by AWS Bedrock or other LLM providers
2. **AWS Cost Insights Plugin** - AWS cost monitoring and optimization

## AWS GenAI Plugin

The GenAI plugin provides an AI-powered chat assistant that can help answer questions about your platform, infrastructure, and documentation.

### Features

- Conversational chat interface
- Integration with AWS Bedrock (Claude models) or OpenAI
- Access to Backstage catalog, entities, and TechDocs
- Tool-use capability for dynamic information retrieval

### Configuration

The plugin is configured in `app-config.yaml`:

```yaml
genai:
  agents:
    general:
      description: General chat assistant for platform engineering
      prompt: >
        You are an expert in platform engineering...
      langgraph:
        messagesMaxTokens: 150000
        bedrock:
          modelId: 'anthropic.claude-3-5-sonnet-20241022-v2:0'
          region: us-west-2
      tools:
        - backstageCatalogSearch
        - backstageEntity
        - backstageTechDocsSearch
```

### AWS Permissions Required

For AWS Bedrock integration, the IAM role/user needs:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "bedrock:InvokeModel",
        "bedrock:InvokeModelWithResponseStream"
      ],
      "Resource": "arn:aws:bedrock:*:*:*"
    }
  ]
}
```

### Using the Assistant

1. Navigate to "AI Assistant" in the sidebar menu
2. Start asking questions about your infrastructure, components, or documentation
3. The assistant can search the catalog and TechDocs to provide relevant answers

### Supported Models

- **AWS Bedrock**: Anthropic Claude 3 (Haiku, Sonnet, Opus)
- **OpenAI**: GPT-4, GPT-3.5 (requires `OPENAI_API_KEY` environment variable)

### Local Development

For local development without AWS credentials, you can use OpenAI:

1. Set the environment variable:
   ```bash
   export OPENAI_API_KEY=your_api_key
   ```

2. Update `app-config.yaml` to use OpenAI instead of Bedrock:
   ```yaml
   genai:
     agents:
       general:
         langgraph:
           openai:
             apiKey: ${OPENAI_API_KEY}
   ```

## AWS Cost Insights Plugin

The Cost Insights plugin provides visibility into AWS infrastructure costs, helping teams understand and optimize their cloud spending.

### Features

- View cost trends over time
- Filter costs by AWS services, tags, or cost categories
- Entity-specific cost views (see costs for individual components/teams)
- Cost grouping by various dimensions

### Configuration

The plugin is configured in `app-config.yaml`:

```yaml
costInsights:
  engineerCost: 200000  # Average engineer cost

aws:
  costInsights:
    costExplorer:
      costMetric: UnblendedCost
    cache:
      enable: true
      defaultTtl: 86400000  # 1 day cache
    entityGroups:
      - kind: Component
        groups:
          - name: service
            type: DIMENSION
            key: SERVICE
          - name: environment
            type: TAG
            key: environment
```

### AWS Permissions Required

The IAM role/user needs:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["ce:GetCostAndUsage"],
      "Resource": "*"
    }
  ]
}
```

### Using Cost Insights

#### Global View

Navigate to "Cost Insights" in the sidebar to see:
- Overall AWS spending trends
- Cost breakdown by service
- Cost comparison over time

#### Entity-Specific View

Add cost tracking to catalog entities using annotations:

```yaml
apiVersion: backstage.io/v1alpha1
kind: Component
metadata:
  name: my-service
  annotations:
    # Filter by AWS resource tags
    aws.amazon.com/cost-insights-tags: component=my-service,environment=prod
    
    # Or use AWS Cost Categories
    # aws.amazon.com/cost-insights-cost-categories: myapp-category=myservice
spec:
  type: service
  lifecycle: production
  owner: platform-team
```

Then view costs in the component's "Cost Insights" tab.

### Cost Allocation Tags

To use tag-based filtering, ensure:

1. **Tag your AWS resources** with consistent tags (e.g., `component`, `environment`, `owner`)
2. **Enable cost allocation tags** in AWS Billing Console
3. Wait 24 hours for tags to become available in Cost Explorer

### Multi-Account Setup

For organizations using consolidated billing:

1. Configure the management account ID:
   ```yaml
   aws:
     costInsights:
       costExplorer:
         accountId: '1111111111'  # Management account ID
   ```

2. Grant appropriate permissions in the management account
3. Tag resources consistently across all accounts

### Cost Optimization

The plugin helps identify:
- Cost trends and anomalies
- Most expensive services/resources
- Cost allocation by team/component
- Opportunities for optimization

## AWS Authentication

### In AWS (ECS/EKS)

When running in AWS:
- Use IAM roles for ECS tasks or EKS service accounts
- Roles are automatically assumed by the application
- No credentials needed in configuration

### Local Development

For local development, use one of:

1. **AWS CLI Profile**
   ```bash
   export AWS_PROFILE=your-profile
   ```

2. **Environment Variables**
   ```bash
   export AWS_ACCESS_KEY_ID=your_key
   export AWS_SECRET_ACCESS_KEY=your_secret
   export AWS_REGION=us-west-2
   ```

3. **AWS SSO**
   ```bash
   aws sso login --profile your-profile
   export AWS_PROFILE=your-profile
   ```

## Troubleshooting

### GenAI Plugin

**Issue**: "Model not found" or "Access denied" errors

**Solution**: 
- Verify you have requested model access in AWS Bedrock console
- Check IAM permissions include `bedrock:InvokeModel`
- Ensure the region is correct in configuration

### Cost Insights Plugin

**Issue**: No cost data displayed

**Solution**:
- Verify cost allocation tags are enabled (takes 24 hours)
- Check IAM permissions include `ce:GetCostAndUsage`
- Ensure entity annotations match actual resource tags
- Clear cache if stale data is displayed

**Issue**: "Rate limit exceeded" errors

**Solution**:
- Each Cost Explorer API call costs $0.01
- Enable caching to reduce API calls
- Increase cache TTL in configuration

## Further Resources

- [AWS Backstage Plugins Repository](https://github.com/awslabs/backstage-plugins-for-aws)
- [GenAI Plugin Documentation](https://github.com/awslabs/backstage-plugins-for-aws/tree/main/plugins/genai)
- [Cost Insights Plugin Documentation](https://github.com/awslabs/backstage-plugins-for-aws/tree/main/plugins/cost-insights)
- [AWS Bedrock Documentation](https://docs.aws.amazon.com/bedrock/)
- [AWS Cost Explorer Documentation](https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html)
