# Oracle

**The Decentralized Prediction Market Resolution Engine**

Built for Solana*

---

## Overview

Oracle is an intelligent market resolution service that automatically determines prediction market outcomes using real-world data. It combines Claude AI's reasoning capabilities with real-time data tools to provide transparent, evidence-based resolutions.

### Key Features

- **AI-Powered Resolution** - Claude AI analyzes multiple data sources to determine outcomes
- **Real-Time Data** - Fetches live prices, news, and web content
- **Transparent Evidence** - Every resolution includes reasoning and sources
- **Multi-Category Support** - Crypto, sports, politics, finance, and more
- **REST API** - Simple integration for any prediction market
- **Solana Native** - Direct on-chain resolution submission

---

## How It Works

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           ORACLE FLOW                                    │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│   1. REQUEST                    2. GATHER                3. RESOLVE      │
│   ─────────                     ──────────               ───────────     │
│                                                                          │
│   ┌──────────┐                 ┌──────────┐            ┌──────────┐     │
│   │ Market   │  ────────────▶  │ Claude   │ ────────▶  │ Decision │     │
│   │ Question │                 │ AI       │            │ + Proof  │     │
│   └──────────┘                 └────┬─────┘            └──────────┘     │
│                                     │                                    │
│                          ┌──────────┼──────────┐                        │
│                          ▼          ▼          ▼                        │
│                     ┌────────┐ ┌────────┐ ┌────────┐                    │
│                     │ Crypto │ │ News   │ │ Web    │                    │
│                     │ Prices │ │ Search │ │ Scrape │                    │
│                     └────────┘ └────────┘ └────────┘                    │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

### Resolution Process

1. **Receive Market** - Oracle receives prediction details (question, deadline, category)
2. **Analyze Question** - AI parses the prediction criteria and determines data needs
3. **Gather Evidence** - Tools fetch real-time data from multiple sources
4. **Cross-Reference** - AI validates data across sources for accuracy
5. **Determine Outcome** - AI decides YES or NO based on evidence
6. **Return Result** - Resolution with confidence score, reasoning, and sources

---

## Supported Categories

| Category | Data Sources | Example Predictions |
|----------|--------------|---------------------|
| **Crypto** | CoinGecko, DexScreener, On-chain | "Will BTC hit $200K by 2025?" |
| **Politics** | News APIs, Official Sources | "Will X win the election?" |
| **Sports** | Sports APIs, News | "Will team X win the championship?" |
| **Finance** | Market APIs, News | "Will AAPL reach $250?" |
| **Tech** | News, Company Announcements | "Will Apple release a foldable iPhone?" |
| **World Events** | News Aggregation | "Will X country join Y alliance?" |
| **Culture** | News, Official Sources | "Will X movie win Best Picture?" |

---

## API Reference

### Base URL

```
Production: https://oracle.example.com/api/v1
Staging:    https://oracle-staging.example.com/api/v1
```

### Authentication

All API requests require an API key in the header:

```
Authorization: Bearer YOUR_API_KEY
```

To obtain an API key, contact: oracle@example.com

---

### Endpoints

#### 1. Resolve Market

Resolve a prediction market and get the outcome.

**Endpoint**: `POST /resolve`

**Request Body**:
```json
{
  "market_id": "string",
  "title": "Will Bitcoin exceed $150,000 by March 2025?",
  "description": "Bitcoin (BTC) price must be above $150,000 USD on CoinGecko at any point before the deadline.",
  "category": "crypto",
  "resolution_deadline": "2025-03-31T23:59:59Z",
  "resolution_criteria": "BTC price > $150,000 USD"
}
```

**Response**:
```json
{
  "success": true,
  "data": {
    "market_id": "string",
    "outcome": "YES",
    "confidence": 0.95,
    "reasoning": "Bitcoin is currently trading at $156,234 on CoinGecko, which exceeds the $150,000 threshold specified in the prediction criteria.",
    "evidence": [
      {
        "source": "CoinGecko",
        "data": "BTC price: $156,234.00 USD",
        "timestamp": "2025-03-15T14:30:00Z"
      },
      {
        "source": "DexScreener",
        "data": "BTC/USDT: $156,189.00",
        "timestamp": "2025-03-15T14:30:00Z"
      }
    ],
    "resolved_at": "2025-03-15T14:30:45Z"
  }
}
```

**Error Response**:
```json
{
  "success": false,
  "error": {
    "code": "INSUFFICIENT_DATA",
    "message": "Unable to gather sufficient evidence to resolve this market",
    "details": "News sources did not return relevant results for the query"
  }
}
```

---

#### 2. Check Resolution Status

Check if a market can be resolved and preview the likely outcome.

**Endpoint**: `POST /check`

**Request Body**:
```json
{
  "title": "Will Ethereum flip Bitcoin by market cap in 2025?",
  "description": "Ethereum's market cap must exceed Bitcoin's market cap.",
  "category": "crypto",
  "resolution_deadline": "2025-12-31T23:59:59Z"
}
```

**Response**:
```json
{
  "success": true,
  "data": {
    "can_resolve": true,
    "preliminary_outcome": "NO",
    "confidence": 0.88,
    "reasoning": "Current market caps: BTC $2.1T, ETH $420B. ETH would need to 5x while BTC stays flat.",
    "data_availability": {
      "crypto_prices": true,
      "news_sources": true,
      "sufficient_evidence": true
    }
  }
}
```

---

#### 3. Get Crypto Price

Fetch current cryptocurrency price.

**Endpoint**: `GET /tools/crypto-price`

**Query Parameters**:
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `symbol` | string | Yes | Token symbol (e.g., "BTC", "ETH", "SOL") |
| `currency` | string | No | Quote currency (default: "USD") |

**Example**: `GET /tools/crypto-price?symbol=BTC&currency=USD`

**Response**:
```json
{
  "success": true,
  "data": {
    "symbol": "BTC",
    "name": "Bitcoin",
    "price": 104523.00,
    "currency": "USD",
    "market_cap": 2100000000000,
    "volume_24h": 45000000000,
    "change_24h": 2.34,
    "last_updated": "2025-01-10T12:00:00Z",
    "source": "CoinGecko"
  }
}
```

---

#### 4. Search News

Search for recent news articles.

**Endpoint**: `GET /tools/news-search`

**Query Parameters**:
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `query` | string | Yes | Search query |
| `days` | number | No | Lookback period (default: 7, max: 30) |
| `limit` | number | No | Max results (default: 10, max: 50) |

**Example**: `GET /tools/news-search?query=bitcoin%20etf%20approval&days=7`

**Response**:
```json
{
  "success": true,
  "data": {
    "query": "bitcoin etf approval",
    "results": [
      {
        "title": "SEC Approves Spot Bitcoin ETF",
        "description": "The Securities and Exchange Commission has officially approved...",
        "url": "https://example.com/article",
        "source": "Reuters",
        "published_at": "2025-01-10T09:00:00Z"
      }
    ],
    "total_results": 45
  }
}
```

---

#### 5. Fetch Webpage

Fetch and parse a webpage for evidence.

**Endpoint**: `POST /tools/fetch-webpage`

**Request Body**:
```json
{
  "url": "https://example.com/article",
  "extract": "text"
}
```

**Response**:
```json
{
  "success": true,
  "data": {
    "url": "https://example.com/article",
    "title": "Article Title",
    "content": "Extracted text content from the webpage...",
    "fetched_at": "2025-01-10T12:00:00Z"
  }
}
```

---

#### 6. Get Token Info (Solana)

Get Solana token information from DexScreener.

**Endpoint**: `GET /tools/token-info`

**Query Parameters**:
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `address` | string | Yes | Token mint address |

**Example**: `GET /tools/token-info?address=So11111111111111111111111111111111111111112`

**Response**:
```json
{
  "success": true,
  "data": {
    "address": "So11111111111111111111111111111111111111112",
    "name": "Wrapped SOL",
    "symbol": "SOL",
    "price_usd": 185.50,
    "market_cap": 85000000000,
    "liquidity": 500000000,
    "volume_24h": 2500000000,
    "price_change_24h": 3.21,
    "source": "DexScreener"
  }
}
```

---

#### 7. Health Check

Check if the Oracle service is operational.

**Endpoint**: `GET /health`

**Response**:
```json
{
  "status": "healthy",
  "version": "1.0.0",
  "uptime": 864000,
  "services": {
    "ai": "operational",
    "crypto_api": "operational",
    "news_api": "operational",
    "database": "operational"
  },
  "last_resolution": "2025-01-10T11:45:00Z"
}
```

---

### Error Codes

| Code | Description |
|------|-------------|
| `INVALID_REQUEST` | Malformed request body or missing required fields |
| `UNAUTHORIZED` | Invalid or missing API key |
| `RATE_LIMITED` | Too many requests, try again later |
| `INSUFFICIENT_DATA` | Unable to gather enough evidence |
| `RESOLUTION_FAILED` | AI could not determine outcome |
| `DEADLINE_NOT_REACHED` | Market resolution deadline hasn't passed |
| `ALREADY_RESOLVED` | Market has already been resolved |
| `INTERNAL_ERROR` | Server error, contact support |

---

## Integration Guide

### 1. Direct API Integration

```typescript
const resolveMarket = async (market: Market) => {
  const response = await fetch('https://oracle.example.com/api/v1/resolve', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${API_KEY}`,
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      market_id: market.id,
      title: market.title,
      description: market.description,
      category: market.category,
      resolution_deadline: market.deadline
    })
  });

  const result = await response.json();

  if (result.success) {
    console.log(`Outcome: ${result.data.outcome}`);
    console.log(`Confidence: ${result.data.confidence}`);
    console.log(`Reasoning: ${result.data.reasoning}`);
  }

  return result;
};
```

### 2. Solana On-Chain Integration

For Solana prediction markets, the Oracle can submit resolutions directly on-chain:

```typescript
import { OracleClient } from '@oracle/sdk';

const oracle = new OracleClient({
  apiKey: API_KEY,
  rpcUrl: 'https://api.mainnet-beta.solana.com',
  programId: ORACLE_PROGRAM_ID
});

// Resolve and submit on-chain
const tx = await oracle.resolveAndSubmit({
  marketPubkey: marketAddress,
  title: market.title,
  description: market.description,
  category: market.category
});

console.log(`Resolution submitted: ${tx.signature}`);
```

### 3. Webhook Integration

Configure webhooks to receive resolution notifications:

```json
POST /webhooks/configure
{
  "url": "https://your-app.com/oracle-webhook",
  "events": ["resolution.completed", "resolution.failed"],
  "secret": "your-webhook-secret"
}
```

Webhook payload:
```json
{
  "event": "resolution.completed",
  "timestamp": "2025-01-10T12:00:00Z",
  "data": {
    "market_id": "abc123",
    "outcome": "YES",
    "confidence": 0.95,
    "reasoning": "...",
    "evidence": [...]
  },
  "signature": "sha256-hmac-signature"
}
```

---

## Rate Limits

| Plan | Requests/min | Requests/day | Resolution/day |
|------|--------------|--------------|----------------|
| Free | 10 | 100 | 10 |
| Starter | 60 | 1,000 | 100 |
| Pro | 300 | 10,000 | 1,000 |
| Enterprise | Custom | Custom | Custom |

Rate limit headers:
```
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 45
X-RateLimit-Reset: 1704891600
```

---

## Best Practices

### Writing Resolvable Predictions

**Good predictions** are:
- Specific and measurable
- Have clear criteria
- Reference verifiable data sources
- Have reasonable timeframes

**Examples**:

| Quality | Prediction | Why |
|---------|------------|-----|
| Good | "Will BTC price exceed $150,000 on CoinGecko by March 31, 2025?" | Specific price, source, deadline |
| Bad | "Will Bitcoin moon?" | Vague, no criteria |
| Good | "Will SpaceX successfully land Starship by Q2 2025?" | Clear event, verifiable |
| Bad | "Will SpaceX be successful?" | Subjective, no timeframe |

### Handling Edge Cases

1. **Ambiguous outcomes** - Include resolution criteria in description
2. **Multiple data sources** - Oracle cross-references automatically
3. **Event cancellation** - Specify handling in prediction description
4. **Time zones** - Use UTC for all deadlines

---

## Security

### Data Integrity
- All resolutions are cryptographically signed
- Evidence is timestamped and archived
- Audit trail for all API calls

### API Security
- TLS 1.3 encryption for all requests
- API keys are hashed and rotated
- IP allowlisting available for Enterprise

### AI Safety
- Multi-source verification prevents manipulation
- Confidence thresholds prevent uncertain resolutions
- Human escalation for edge cases

---

## Changelog

### v1.0.0 (January 2026)
- Initial release
- Crypto price resolution
- News search integration
- Web scraping tools
- Solana on-chain submission

---

*Oracle - Trustless Resolution for Prediction Markets*
