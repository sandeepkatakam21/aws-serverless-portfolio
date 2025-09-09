# Serverless URL Shortener

**Version: v1.0.0 – September 2025**

🔗 A scalable, production-ready URL shortening service built on AWS serverless technologies

## Overview

This project demonstrates a comprehensive serverless URL shortening service leveraging AWS cloud technologies including API Gateway, Lambda, DynamoDB, and S3. Designed with enterprise-grade scalability, cost-efficiency, and reliability in mind, this solution provides both basic URL shortening capabilities and advanced features for professional use cases.

## Architecture

### System Overview
```
┌─────────────────┐    ┌──────────────┐    ┌─────────────────┐
│   Frontend      │    │ API Gateway  │    │   Lambda        │
│   (S3 + CF)     │───▶│   REST API   │───▶│   Functions     │
│                 │    │              │    │                 │
└─────────────────┘    └──────────────┘    └─────────────────┘
                                                    │
                                                    ▼
┌─────────────────┐    ┌──────────────┐    ┌─────────────────┐
│   CloudWatch    │    │ DynamoDB     │    │   S3 Bucket     │
│   Monitoring    │◀───│   NoSQL DB   │◀───│   Analytics     │
│                 │    │              │    │                 │
└─────────────────┘    └──────────────┘    └─────────────────┘
```

### Technology Stack
- **API Gateway**: RESTful API endpoints with authentication and rate limiting
- **AWS Lambda**: Serverless compute for business logic (Node.js/Python)
- **DynamoDB**: NoSQL database for URL mappings with Global Secondary Index
- **S3**: Static website hosting and analytics data storage
- **CloudFront**: Global CDN for performance optimization
- **Route 53**: Custom domain management
- **CloudWatch**: Monitoring, logging, and alerting
- **AWS Cognito**: User authentication and OAuth integration

## Features

### Core Features
- ✅ **URL Shortening**: Convert long URLs to short, memorable links
- ✅ **Analytics Tracking**: Comprehensive click analytics and user behavior insights
- ✅ **Custom Short Codes**: User-defined aliases for branded links
- ✅ **Expiration Dates**: Time-limited links with automatic cleanup
- ✅ **QR Code Generation**: Automatic QR codes for mobile sharing
- ✅ **Rate Limiting**: API protection against abuse and spam
- ✅ **HTTPS Support**: Secure connections with SSL certificates
- ✅ **Bulk Operations**: Process multiple URLs simultaneously

### Advanced Features
- 🚀 **Custom Domains**: Brand your shortened URLs with your own domain
- 🔐 **OAuth Login Integration**: Google, GitHub, and Microsoft authentication
- 🌙 **Dark Theme Analytics Dashboard**: Modern, responsive UI with theme switching
- 📊 **Link Preview API**: Rich metadata extraction and social media previews
- 📢 **Slack/Webhook Integration**: Real-time notifications and team collaboration
- 📈 **Export Analytics CSV**: Detailed reporting and data export capabilities
- 🔄 **A/B Testing Support**: Split traffic for conversion optimization
- 🌍 **Geolocation Analytics**: Country and region-based insights
- 📱 **Mobile App Integration**: RESTful APIs for mobile applications
- 🔒 **Password Protection**: Secure links with custom passwords

## Getting Started

### Prerequisites
- AWS CLI configured with appropriate permissions
- Node.js 18.x or later
- Serverless Framework or AWS SAM CLI
- Domain name (optional, for custom domains)

### Quick Deployment

1. **Clone the repository**
   ```bash
   git clone https://github.com/sandeepkatakam21/aws-serverless-portfolio.git
   cd aws-serverless-portfolio/lambda-functions/url-shortener
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Configure environment variables**
   ```bash
   cp .env.example .env
   # Edit .env with your AWS region, domain, and other settings
   ```

4. **Deploy to AWS**
   ```bash
   # Using Serverless Framework
   serverless deploy
   
   # Or using AWS SAM
   sam build && sam deploy --guided
   ```

5. **Setup custom domain (optional)**
   ```bash
   aws route53 create-hosted-zone --name your-domain.com
   # Follow the domain configuration guide in /docs/custom-domains.md
   ```

### API Endpoints

```http
POST   /api/v1/shorten     # Create shortened URL
GET    /api/v1/{shortCode} # Redirect to original URL
GET    /api/v1/analytics/{shortCode} # Get link analytics
DELETE /api/v1/{shortCode} # Delete shortened URL
POST   /api/v1/bulk        # Bulk URL operations
GET    /api/v1/preview/{shortCode} # Get link preview data
```

## Advanced Use Cases

### Marketing Campaign Tracking
```javascript
// Create campaign-specific links with UTM parameters
const campaignLink = await createShortUrl({
  originalUrl: 'https://example.com?utm_source=email&utm_campaign=summer2025',
  customCode: 'summer-sale',
  expirationDate: '2025-09-30',
  password: 'campaign123'
});
```

### E-commerce Integration
```javascript
// Generate product links with A/B testing
const productLinks = await bulkCreateUrls([
  { url: 'https://store.com/product/123?variant=A', code: 'product-a' },
  { url: 'https://store.com/product/123?variant=B', code: 'product-b' }
]);
```

### Social Media Automation
```javascript
// Slack webhook integration for team notifications
const slackIntegration = {
  webhook: 'https://hooks.slack.com/services/YOUR/SLACK/WEBHOOK',
  channel: '#marketing',
  events: ['link_created', 'milestone_reached', 'high_traffic_alert']
};
```

### Content Creator Workflow
```javascript
// Create branded links with analytics export
const creatorLink = await createShortUrl({
  originalUrl: 'https://youtube.com/watch?v=example',
  customDomain: 'creator.link',
  enablePreview: true,
  trackConversions: true,
  exportSchedule: 'weekly'
});
```

## Troubleshooting

### Common Issues

#### API Rate Limits
- **Problem**: `429 Too Many Requests` errors
- **Solution**: Implement exponential backoff and request batching
- **Prevention**: Monitor usage patterns and consider upgrading limits

```javascript
// Implement retry logic with exponential backoff
const retryWithBackoff = async (fn, retries = 3) => {
  for (let i = 0; i < retries; i++) {
    try {
      return await fn();
    } catch (error) {
      if (error.status === 429 && i < retries - 1) {
        await new Promise(resolve => setTimeout(resolve, Math.pow(2, i) * 1000));
        continue;
      }
      throw error;
    }
  }
};
```

#### CORS Configuration Issues
- **Problem**: Cross-origin requests blocked
- **Solution**: Update API Gateway CORS settings
- **Best Practice**: Use environment-specific allowed origins

```yaml
# serverless.yml CORS configuration
functions:
  shortenUrl:
    events:
      - http:
          path: api/v1/shorten
          method: post
          cors:
            origin: ${env:ALLOWED_ORIGINS}
            headers:
              - Content-Type
              - Authorization
```

#### DynamoDB Performance
- **Problem**: Read/write throttling
- **Solution**: Implement auto-scaling and optimize partition keys
- **Monitoring**: Set CloudWatch alarms for throttling events

### Cost Optimization Best Practices

1. **Lambda Optimization**
   - Use appropriate memory allocation (128MB-512MB for most cases)
   - Implement connection pooling for database connections
   - Enable provisioned concurrency only for high-traffic endpoints

2. **DynamoDB Cost Management**
   - Use on-demand pricing for unpredictable workloads
   - Implement TTL for expired links
   - Archive old analytics data to S3

3. **CloudWatch Logging**
   - Set appropriate log retention periods (7-30 days)
   - Use structured logging to reduce log volume
   - Implement log sampling for high-volume endpoints

### Error Resolution Guide

| Error Code | Description | Resolution |
|------------|-------------|------------|
| `INVALID_URL` | Malformed URL provided | Validate URL format before submission |
| `CODE_EXISTS` | Custom code already in use | Choose different code or use auto-generation |
| `EXPIRED_LINK` | Link has exceeded expiration | Create new link or extend expiration |
| `RATE_LIMITED` | Too many requests | Implement backoff strategy |
| `DOMAIN_ERROR` | Custom domain configuration issue | Check DNS settings and SSL certificates |

## Changelog

### Version 1.0.0 - September 2025

#### 🎉 Initial Release
- ✅ Core URL shortening functionality with DynamoDB storage
- ✅ RESTful API with comprehensive endpoint coverage
- ✅ Real-time analytics tracking and reporting
- ✅ Custom short code generation and validation
- ✅ QR code generation for mobile sharing
- ✅ Bulk URL processing capabilities

#### 🚀 Advanced Features
- ✅ Custom domain support with Route 53 integration
- ✅ OAuth authentication (Google, GitHub, Microsoft)
- ✅ Dark theme analytics dashboard with responsive design
- ✅ Link preview API with metadata extraction
- ✅ Slack and webhook integration for notifications
- ✅ CSV export functionality for analytics data
- ✅ A/B testing support for conversion optimization
- ✅ Geolocation analytics and insights
- ✅ Password protection for secure links

#### 🛠️ Infrastructure & DevOps
- ✅ Comprehensive CloudWatch monitoring and alerting
- ✅ Auto-scaling configuration for high availability
- ✅ CI/CD pipeline with automated testing
- ✅ Infrastructure as Code with Serverless Framework
- ✅ Security best practices and IAM role management
- ✅ Cost optimization features and monitoring

#### 📚 Documentation & Support
- ✅ Comprehensive API documentation with examples
- ✅ Advanced use case scenarios and workflows
- ✅ Troubleshooting guide with common solutions
- ✅ Performance optimization recommendations
- ✅ Security configuration guidelines

## License

MIT License - see the [LICENSE](LICENSE) file for details.

## References

- [AWS Serverless Application Model (SAM)](https://docs.aws.amazon.com/serverless-application-model/)
- [AWS Lambda Best Practices](https://docs.aws.amazon.com/lambda/latest/dg/best-practices.html)
- [DynamoDB Best Practices](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/best-practices.html)
- [API Gateway Documentation](https://docs.aws.amazon.com/apigateway/)
- [CloudWatch Monitoring Guide](https://docs.aws.amazon.com/cloudwatch/)
- [Serverless Framework Documentation](https://www.serverless.com/framework/docs/)

---

**Built with ❤️ using AWS Serverless Technologies**

For questions, issues, or contributions, please visit our [GitHub Issues](https://github.com/sandeepkatakam21/aws-serverless-portfolio/issues) page.
