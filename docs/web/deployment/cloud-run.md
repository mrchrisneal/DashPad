---
title: Deployment - Google Cloud Run (DashPad-Web)
---

# Deployment - Google Cloud Run

Deploy the DashPad-Web container to Google Cloud Run for cloud-based dashboard access.

!!! warning "Only the Web container can be deployed to Cloud Run. The API container must be run on actual servers, as it needs direct filesystem access to collect metrics and system information."

## Prerequisites

- Google Cloud account with billing enabled
- `gcloud` CLI installed
- API containers running on your servers
- Domain name (optional)

## Deployment Steps

### 1. Set Up Project

```bash
# Set project variables
export PROJECT_ID="your-project-id"
export REGION="us-central1"

# Enable required APIs
gcloud services enable run.googleapis.com
```

### 2. Build and Deploy Web Container

```bash
# Clone repository
git clone https://github.com/mrchrisneal/DashPad-Web
cd DashPad-Web

# Build and submit to Cloud Build
gcloud builds submit --tag gcr.io/$PROJECT_ID/dashpad-web

# Deploy to Cloud Run
gcloud run deploy dashpad-web \
  --image gcr.io/$PROJECT_ID/dashpad-web \
  --platform managed \
  --region $REGION \
  --allow-unauthenticated \
  --port 80 \
  --set-env-vars "DASHPAD_SERVERS=http://your-server:8000|Server Name"
```

### 3. Configure Environment Variables

Set your server configurations when deploying:

```bash
# Single server
gcloud run deploy dashpad-web \
  --set-env-vars "LISTEN_PORT=443,USE_HTTPS=true,AUTH_USERNAME=admin,AUTH_PASSWORD=password,SERVER1_NAME=Home Server,SERVER1_URL=https://your-server:5241,SERVER1_KEY=your-api-key,SERVER1_SSLFINGERPRINT=your-ssl-fingerprint"

# Multiple servers
gcloud run deploy dashpad-web \
  --set-env-vars "LISTEN_PORT=443,USE_HTTPS=true,AUTH_USERNAME=admin,AUTH_PASSWORD=password,SERVER1_NAME=Server 1,SERVER1_URL=https://server1:5241,SERVER1_KEY=key1,SERVER1_SSLFINGERPRINT=fp1,SERVER2_NAME=Server 2,SERVER2_URL=https://server2:5241,SERVER2_KEY=key2,SERVER2_SSLFINGERPRINT=fp2"
```

## Custom Domain (Optional)

Map a custom domain:

```bash
gcloud run domain-mappings create \
  --service dashpad-web \
  --domain your-domain.com \
  --region $REGION
```

Update DNS records as instructed.

## Security Considerations

### API Access

Your API containers must be accessible from Cloud Run:
- Use public IPs with strong API keys
- Set up Cloud VPN for private access
- Use a reverse proxy with authentication

### Authentication

The Web container includes basic authentication:

```bash
--set-env-vars "AUTH_USERNAME=admin,AUTH_PASSWORD=secure-password"
```

API authentication is handled via individual API keys per server.

## Cost Optimization

Cloud Run bills per request and compute time:
- Set minimum instances to 0
- Use appropriate memory allocation (128MB usually sufficient)
- Consider using Cloud CDN for static assets

Typical cost: ~$0.35/month for light usage

## Updating

To update the deployed container:

```bash
# Rebuild and deploy
gcloud builds submit --tag gcr.io/$PROJECT_ID/dashpad-web
gcloud run deploy dashpad-web --image gcr.io/$PROJECT_ID/dashpad-web
```

## Next Steps

- Ensure your API containers are accessible
- Configure authentication on both API and Web
- Set up monitoring for the Cloud Run service