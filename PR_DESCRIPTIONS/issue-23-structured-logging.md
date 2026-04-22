# Add Structured Logging with Winston - Closes #23

## 📋 Description

This PR replaces all `console.log` statements with a production-ready structured logging system using Winston. This enables proper log aggregation, filtering, and monitoring in production environments.

## 🚀 Changes Made

### Winston Logger Implementation

- **Created `src/utils/logger.ts`** with comprehensive Winston configuration
- **Replaced all console statements** with structured logger calls
- **Environment-aware formatting:**
  - **Production**: JSON format for log aggregation tools (ELK, Splunk, etc.)
  - **Development**: Pretty-print format for readability

### Request Tracking System

- **Request ID middleware** generates unique UUID for each request
- **Request-scoped logging** with child loggers
- **X-Request-ID header** added to all responses for tracing
- **Automatic request logging** with method, path, and context

### Configurable Log Levels

- **LOG_LEVEL environment variable** controls verbosity
- **Supported levels**: `debug`, `info`, `warn`, `error`
- **Default**: `info` level
- **Timestamp** included in all log entries

## 📊 Log Format Examples

### Development Format (Pretty-Print)
```
2026-04-22 19:35:00 [info]: StellarKraal API running on port 3001 {
  "port": 3001,
  "environment": "development",
  "logLevel": "info"
}

2026-04-22 19:35:05 [info]: POST /api/collateral/register {
  "method": "POST",
  "path": "/api/collateral/register",
  "requestId": "550e8400-e29b-41d4-a716-446655440000"
}
```

### Production Format (JSON)
```json
{
  "timestamp": "2026-04-22T19:35:00.000Z",
  "level": "info",
  "message": "StellarKraal API running on port 3001",
  "port": 3001,
  "environment": "production",
  "logLevel": "info"
}

{
  "timestamp": "2026-04-22T19:35:05.000Z",
  "level": "info", 
  "message": "POST /api/collateral/register",
  "method": "POST",
  "path": "/api/collateral/register",
  "requestId": "550e8400-e29b-41d4-a716-446655440000"
}
```

## ✅ Acceptance Criteria Met

- ✅ **Winston installed and configured**
- ✅ **All console.log/error/warn replaced with logger calls**
- ✅ **Log level configurable via LOG_LEVEL env var**
- ✅ **JSON-formatted output in production, pretty-print in development**

## 🔧 Configuration

### Environment Variables
```bash
# Optional - defaults to 'info'
LOG_LEVEL=debug|info|warn|error

# Optional - affects log format
NODE_ENV=development|production
```

### Usage Examples
```typescript
import logger, { createRequestLogger } from './utils/logger';

// Basic logging
logger.info('Server started', { port: 3001 });
logger.error('Operation failed', { error: err.message });

// Request-scoped logging
const reqLogger = createRequestLogger(requestId);
reqLogger.info('Processing request', { userId: 123 });
```

## 🧪 Testing

### Unit Tests Added
- Logger mocking in test environment
- Request ID middleware functionality
- X-Request-ID header validation
- All endpoints maintain functionality with new logging

### Manual Testing
```bash
# Start server and observe structured logs
npm run dev

# Make requests and see request tracking
curl -X POST http://localhost:3001/api/collateral/register \
  -H "Content-Type: application/json" \
  -d '{"owner":"GABC...","animal_type":"cattle","count":5,"appraised_value":1000000}'

# Check X-Request-ID header in response
curl -I http://localhost:3001/api/health
```

## 📈 Production Benefits

### Log Aggregation Ready
- **ELK Stack**: Elasticsearch, Logstash, Kibana
- **Splunk**: Direct JSON ingestion
- **CloudWatch**: AWS log aggregation
- **Datadog**: Application monitoring

### Debugging & Monitoring
- **Request tracing** with unique IDs
- **Structured queries** on log data
- **Error correlation** across requests
- **Performance monitoring** with timestamps

### Alerting Capabilities
- **Error rate monitoring** on `error` level logs
- **Performance alerts** on slow requests
- **Circuit breaker events** (future integration)
- **Custom business logic alerts**

## 📁 Files Changed

- `backend/src/utils/logger.ts` ✨ **NEW** - Winston logger configuration
- `backend/src/index.ts` - Integrated logger and request tracking
- `backend/src/index.test.ts` - Added logger mocking and tests
- `backend/package.json` - Added Winston dependency

## 🔗 Dependencies Added

```json
{
  "winston": "^3.11.0"
}
```

## 🔗 Related Issues

Closes #23

## 📝 Migration Notes

### Before (Console Logging)
```javascript
console.log('Server started on port', PORT);
console.error('Error occurred:', error);
```

### After (Structured Logging)
```javascript
logger.info('Server started', { port: PORT });
logger.error('Error occurred', { error: error.message, stack: error.stack });
```

## 🚀 Future Enhancements

This logging foundation enables:
- **Metrics collection** (request counts, response times)
- **Distributed tracing** integration
- **Custom log processors** for business events
- **Real-time monitoring dashboards**