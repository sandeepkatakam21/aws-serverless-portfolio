# Serverless URL Shortener

🔗 **A scalable, serverless URL shortening service built on AWS**

This project demonstrates a production-ready URL shortening service leveraging AWS serverless technologies including API Gateway, Lambda, DynamoDB, and S3. Built with scalability, cost-efficiency, and reliability in mind.

## 🏗️ Architecture

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
- **CloudFront**: CDN for global content delivery
- **CloudWatch**: Monitoring, logging, and alerting
- **IAM**: Security and access control

### Data Flow

1. **URL Shortening**:
   - User submits long URL via API
   - Lambda generates unique short code
   - URL mapping stored in DynamoDB
   - Returns shortened URL

2. **URL Redirection**:
   - User accesses short URL
   - API Gateway routes to Lambda
   - Lambda queries DynamoDB
   - Analytics logged to S3
   - HTTP 301/302 redirect to original URL

3. **Analytics Processing**:
   - Click events stored in DynamoDB
   - Batch processing via scheduled Lambda
   - Aggregated data stored in S3
   - Dashboard visualization

## 🎯 Features

### Core Functionality
- ✅ **URL Shortening**: Generate short, memorable URLs
- ✅ **Custom Aliases**: User-defined short codes
- ✅ **Expiration Dates**: Time-limited URLs
- ✅ **Bulk Operations**: Process multiple URLs
- ✅ **QR Code Generation**: Visual URL representation

### Analytics & Monitoring
- 📊 **Click Tracking**: Detailed analytics per URL
- 🌍 **Geographic Data**: Visitor location insights
- 📱 **Device Analytics**: Browser and device statistics
- ⏰ **Time-based Metrics**: Usage patterns over time
- 🚨 **Real-time Alerts**: Performance monitoring

### Security & Compliance
- 🔐 **API Authentication**: JWT and API key support
- 🛡️ **Rate Limiting**: DDoS protection
- 🚫 **URL Validation**: Malicious link detection
- 🔒 **HTTPS Enforcement**: Secure connections only
- 📋 **Audit Logging**: Complete activity trails

## 🚀 Use Cases

### Business Applications

1. **Marketing Campaigns**
   - Track campaign performance
   - A/B test different URLs
   - Social media link management
   - Email marketing optimization

2. **Content Management**
   - Simplify complex URLs for sharing
   - Track document access patterns
   - Temporary promotional links
   - Event registration management

3. **Analytics & Insights**
   - User behavior analysis
   - Geographic targeting data
   - Conversion funnel tracking
   - ROI measurement

4. **API Integration**
   - Third-party service integration
   - Webhook URL management
   - Dynamic redirect handling
   - Multi-tenant URL management

### Technical Benefits

- **Serverless Scalability**: Auto-scaling to handle traffic spikes
- **Cost Efficiency**: Pay only for actual usage
- **High Availability**: Multi-AZ deployment with 99.9% uptime
- **Global Performance**: CloudFront edge locations
- **Security**: AWS-native security controls

## 🛠️ Getting Started

### Prerequisites

```bash
# Required tools
- AWS CLI v2+
- AWS SAM CLI
- Node.js 18+ or Python 3.9+
- Docker (for local testing)
- Git
```

### Quick Setup

#### 1. Clone and Setup

```bash
# Clone the repository
git clone https://github.com/sandeepkatakam21/aws-serverless-portfolio.git
cd aws-serverless-portfolio/lambda-functions/url-shortener

# Install dependencies
npm install  # For Node.js
# OR
pip install -r requirements.txt  # For Python
```

#### 2. Configure AWS Credentials

```bash
# Configure AWS CLI
aws configure
# Enter your AWS Access Key ID, Secret, and Region

# Verify configuration
aws sts get-caller-identity
```

#### 3. Deploy Infrastructure

```bash
# Build the application
sam build

# Deploy with guided setup (first time)
sam deploy --guided

# For subsequent deployments
sam deploy
```

#### 4. Environment Variables

Create a `.env` file:

```bash
# AWS Configuration
AWS_REGION=us-east-1
STAGE=dev

# DynamoDB
URL_TABLE_NAME=url-shortener-urls
ANALYTICS_TABLE_NAME=url-shortener-analytics

# S3
STATIC_BUCKET=url-shortener-frontend
ANALYTICS_BUCKET=url-shortener-data

# Domain Configuration
BASE_DOMAIN=short.ly  # Your custom domain
API_DOMAIN=api.short.ly

# Security
JWT_SECRET=your-jwt-secret-key
API_KEY_REQUIRED=true

# Analytics
ENABLE_ANALYTICS=true
RETENTION_DAYS=90
```

### 🧪 Testing

#### Local Testing

```bash
# Start local API
sam local start-api

# Test URL shortening
curl -X POST http://localhost:3000/shorten \
  -H "Content-Type: application/json" \
  -d '{"url": "https://example.com", "customAlias": "demo"}'

# Test URL resolution
curl -I http://localhost:3000/demo
```

#### API Endpoints

```bash
# Shorten URL
POST /shorten
Body: {
  "url": "https://example.com",
  "customAlias": "optional-alias",
  "expirationDate": "2024-12-31"
}

# Get URL info
GET /info/{shortCode}

# Get analytics
GET /analytics/{shortCode}

# Bulk operations
POST /bulk
Body: {
  "urls": [
    {"url": "https://example1.com"},
    {"url": "https://example2.com"}
  ]
}

# Redirect (user-facing)
GET /{shortCode}
```

### 📊 Monitoring

#### CloudWatch Dashboards

- **Performance Metrics**: Response times, error rates
- **Usage Statistics**: Requests per minute, active URLs
- **Cost Analysis**: Function invocations, data transfer
- **Security Monitoring**: Authentication failures, rate limits

#### Custom Alarms

```bash
# High error rate
ErrorRate > 5% for 2 consecutive periods

# Performance degradation
Average response time > 2 seconds

# Usage spikes
Requests per minute > 1000

# Storage costs
DynamoDB consumed capacity > 80%
```

## 📈 Performance Optimization

### Cold Start Optimization

- **Provisioned Concurrency**: Pre-warmed Lambda instances
- **Connection Pooling**: Reuse database connections
- **Dependency Management**: Minimize bundle size
- **Layer Utilization**: Shared dependencies

### Cost Optimization

- **DynamoDB On-Demand**: Pay per request pricing
- **S3 Intelligent Tiering**: Automatic cost optimization
- **Lambda Memory Tuning**: Right-sizing for performance/cost
- **CloudFront Caching**: Reduce origin requests

## 🔧 Configuration Options

### DynamoDB Schema

```javascript
// URLs Table
PrimaryKey: shortCode (String)
Attributes: {
  longUrl: String,
  createdAt: Number,
  expirationDate: Number,
  clickCount: Number,
  userId: String,
  isActive: Boolean
}

// GSI: userId-createdAt-index
// GSI: createdAt-index (for analytics)

// Analytics Table
PrimaryKey: shortCode (String)
SortKey: timestamp (Number)
Attributes: {
  ipAddress: String,
  userAgent: String,
  referer: String,
  country: String,
  device: String
}
```

### API Gateway Configuration

```yaml
# Rate Limiting
Throttling:
  BurstLimit: 200
  RateLimit: 100

# CORS Configuration
CORS:
  AllowOrigins: ['*']
  AllowMethods: ['GET', 'POST', 'OPTIONS']
  AllowHeaders: ['Content-Type', 'Authorization']

# Authentication
Authorizers:
  - JWTAuthorizer
  - APIKeyAuthorizer
```

## 📚 Additional Resources

### Documentation
- [AWS Lambda Best Practices](https://docs.aws.amazon.com/lambda/latest/dg/best-practices.html)
- [DynamoDB Design Patterns](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-modeling-nosql.html)
- [API Gateway Performance](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-request-throttling.html)

### Related Projects
- [Serverless Contact Form](../contact-form-backend/)
- [Real-time Chat API](../chat-api/)
- [File Upload Service](../file-upload-service/)

## 🤝 Contributing

Contributions are welcome! Please read our [Contributing Guidelines](../../CONTRIBUTING.md) and submit pull requests for any improvements.

### Development Setup

```bash
# Fork and clone
git clone https://github.com/your-username/aws-serverless-portfolio.git

# Create feature branch
git checkout -b feature/url-shortener-enhancement

# Make changes and test
npm test

# Submit pull request
git push origin feature/url-shortener-enhancement
```

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](../../LICENSE) file for details.

## 🙏 Acknowledgments

- AWS Serverless Application Repository
- AWS Well-Architected Framework
- Serverless Framework Community
- URL shortening algorithm inspirations

---

**Built with ❤️ using AWS Serverless Technologies**

*Ready to shorten your URLs? Deploy this solution and start tracking your links!*
