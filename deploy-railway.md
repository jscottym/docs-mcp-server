# Railway Deployment Guide

This guide explains how to deploy the docs-mcp-server on Railway with persistent storage.

## Prerequisites

1. A [Railway](https://railway.app) account
2. [Railway CLI](https://docs.railway.app/develop/cli) installed (optional, but recommended)
3. An OpenAI API key (or other supported embedding provider)

## Quick Deploy

### Option 1: One-Click Deploy (Recommended)

[![Deploy on Railway](https://railway.app/button.svg)](https://railway.app/template/your-template-id)

### Option 2: Manual Deployment

1. **Fork or clone this repository**

2. **Connect to Railway:**

   ```bash
   railway login
   railway init
   ```

3. **Add a volume for persistent storage:**

   ```bash
   railway volume add --mount-path /app/data
   ```

4. **Set environment variables:**

   ```bash
   railway variables set OPENAI_API_KEY=your_openai_api_key_here
   ```

5. **Deploy:**
   ```bash
   railway up
   ```

## Environment Variables

Copy `.env.example` to `.env` and configure the following variables in Railway:

### Required

- `OPENAI_API_KEY`: Your OpenAI API key

### Optional

- `DOCS_MCP_EMBEDDING_MODEL`: Embedding model (default: `text-embedding-3-small`)
- `OPENAI_API_BASE`: Custom OpenAI-compatible endpoint
- `DOCS_MCP_STORE_PATH`: Data directory (auto-set to `/app/data`)

### Alternative Embedding Providers

See `.env.example` for configuration options for:

- Google AI (Gemini)
- Google Cloud Vertex AI
- AWS Bedrock
- Azure OpenAI

## Volume Configuration

Railway volumes provide persistent storage for your SQLite database. The application is configured to store data in `/app/data` which should be mounted as a Railway volume.

### Adding a Volume

**Via Railway Dashboard:**

1. Go to your service settings
2. Navigate to "Volumes"
3. Click "New Volume"
4. Set mount path to `/app/data`
5. Choose volume size (5GB recommended minimum)

**Via CLI:**

```bash
railway volume add --mount-path /app/data --size 5
```

## Service Configuration

The Railway deployment runs both the MCP server and web interface in a single container. You can access:

- **MCP Server**: Available on the Railway-assigned port (environment variable `PORT`)
- **Web Interface**: Available at your Railway app URL

## Custom Domains

To use a custom domain:

1. Go to your Railway project settings
2. Navigate to "Domains"
3. Add your custom domain
4. Configure DNS records as instructed

## Monitoring and Logs

Monitor your deployment:

- **Logs**: `railway logs`
- **Status**: Railway dashboard
- **Metrics**: Available in Railway dashboard

## Troubleshooting

### Common Issues

1. **Volume mounting errors**: Ensure the volume is mounted to `/app/data`
2. **Environment variables**: Verify `OPENAI_API_KEY` is set
3. **Build failures**: Check that `Dockerfile.railway` is being used

### Getting Help

- Check Railway logs: `railway logs`
- Review environment variables: `railway variables`
- Verify volume configuration in Railway dashboard

## Scaling

Railway automatically handles:

- Horizontal scaling based on traffic
- Resource allocation
- Load balancing

For high-traffic deployments, consider:

- Increasing volume size
- Using Railway's Pro plan for better performance
- Monitoring resource usage

## Cost Optimization

- Use Railway's sleep mode for development environments
- Monitor volume usage and clean up old data periodically
- Consider using Railway's usage-based pricing

## Next Steps

After deployment:

1. **Test the deployment**: Visit your Railway app URL
2. **Configure your MCP client**: Use the Railway URL in your MCP settings
3. **Add documentation**: Use the web interface to scrape documentation
4. **Monitor usage**: Check Railway dashboard for metrics

## Migration from Docker Compose

If migrating from local Docker Compose:

1. Export your data volume:

   ```bash
   docker run --rm -v docs-mcp-data:/data -v $(pwd):/backup alpine tar czf /backup/data-backup.tar.gz -C /data .
   ```

2. Deploy to Railway (follow steps above)

3. Upload your data:
   ```bash
   railway run bash
   # In the Railway shell:
   cd /app/data
   # Upload and extract your backup
   ```

## Support

For Railway-specific issues:

- [Railway Documentation](https://docs.railway.app)
- [Railway Community](https://help.railway.app)

For application issues:

- Check the main README.md
- Review application logs
