# WebSocket API Reference

FunPump's WebSocket API enables real-time communication for virtual environments, trading, and voice interactions. This guide details the available message types and their usage.

## Connection

### Endpoint
```
ws://{host}/ws
wss://{host}/ws  // Secure connection
```

### Connection Management
```typescript
const ws = useWebSocket({
  reconnectAttempts: 5,
  reconnectInterval: 3000,
  heartbeatInterval: 30000
});
```

## Message Types

All messages follow this structure:
```typescript
interface WebSocketMessage {
  type: string;
  data: any;
}
```

### Virtual Environment Messages

#### 1. Join Environment
```typescript
// Client -> Server
{
  type: 'JOIN_ENVIRONMENT',
  data: {
    environmentId: string;
    userId: string;  // Wallet public key
  }
}

// Server -> Client (Success)
{
  type: 'ENVIRONMENT_STATE',
  data: {
    id: string;
    name: string;
    tokenMint?: string;
    virtualSolReserves: string;  // Bigint as string
    virtualTokenReserves: string;
    participants: number;
    chatHistory: ChatMessage[];
    trades: VirtualTrade[];
  }
}
```

#### 2. Execute Virtual Trade
```typescript
// Client -> Server
{
  type: 'VIRTUAL_TRADE',
  data: {
    environmentId: string;
    amount: string;  // Bigint as string
    isBuy: boolean;
  }
}

// Server -> Client (Success)
{
  type: 'VIRTUAL_TRADE_EXECUTED',
  data: {
    trade: {
      userId: string;
      amount: string;
      price: string;
      timestamp: number;
      isBuy: boolean;
    },
    newReserves: {
      sol: string;
      token: string;
    }
  }
}
```

#### 3. Chat Messages
```typescript
// Client -> Server
{
  type: 'CHAT_MESSAGE',
  data: {
    environmentId: string;
    message: string;
  }
}

// Server -> Client
{
  type: 'CHAT_MESSAGE',
  data: {
    userId: string;
    message: string;
    timestamp: number;
  }
}
```

### Voice Command Messages

#### 1. Start Voice Session
```typescript
// Client -> Server
{
  type: 'VOICE_SESSION_START',
  data: {
    userId: string;
    preferredLanguage?: string;
  }
}

// Server -> Client
{
  type: 'VOICE_SESSION_READY',
  data: {
    sessionId: string;
    liveKitToken: string;  // For audio streaming
  }
}
```

#### 2. Voice Command Processing
```typescript
// Server -> Client (Transcription)
{
  type: 'VOICE_TRANSCRIPTION',
  data: {
    text: string;
    isFinal: boolean;
  }
}

// Server -> Client (Command Recognition)
{
  type: 'VOICE_COMMAND',
  data: {
    intent: string;
    parameters: Record<string, any>;
    confidence: number;
  }
}
```

### Price Updates

#### 1. Subscribe to Price Updates
```typescript
// Client -> Server
{
  type: 'SUBSCRIBE_PRICE',
  data: {
    mintAddress: string;
    environmentId?: string;  // For virtual environments
  }
}

// Server -> Client
{
  type: 'PRICE_UPDATE',
  data: {
    mintAddress: string;
    price: string;
    volume: string;
    timestamp: number;
  }
}
```

### Reward System Updates

#### 1. Subscribe to Rewards
```typescript
// Client -> Server
{
  type: 'SUBSCRIBE_REWARDS',
  data: {
    userId: string;  // Wallet public key
  }
}

// Server -> Client
{
  type: 'REWARD_UPDATE',
  data: {
    tier: string;
    unclaimedRewards: string;
    totalLaunches: number;
    totalVolume: string;
  }
}
```

## Error Handling

### Error Message Format
```typescript
{
  type: 'ERROR',
  data: {
    code: string;
    message: string;
    details?: any;
  }
}
```

### Common Error Codes
```typescript
enum ErrorCode {
  INVALID_MESSAGE = 'INVALID_MESSAGE',
  UNAUTHORIZED = 'UNAUTHORIZED',
  ENVIRONMENT_NOT_FOUND = 'ENVIRONMENT_NOT_FOUND',
  INSUFFICIENT_BALANCE = 'INSUFFICIENT_BALANCE',
  RATE_LIMITED = 'RATE_LIMITED'
}
```

## Client Implementation

### Connection Setup
```typescript
class WebSocketClient {
  private ws: WebSocket | null = null;
  private reconnectAttempts = 0;
  private maxReconnectAttempts = 5;

  connect() {
    this.ws = new WebSocket(WS_ENDPOINT);
    
    this.ws.onopen = () => {
      this.reconnectAttempts = 0;
      this.startHeartbeat();
    };

    this.ws.onclose = () => {
      if (this.reconnectAttempts < this.maxReconnectAttempts) {
        setTimeout(() => this.connect(), 3000);
        this.reconnectAttempts++;
      }
    };

    this.ws.onmessage = this.handleMessage.bind(this);
  }

  private startHeartbeat() {
    setInterval(() => {
      if (this.ws?.readyState === WebSocket.OPEN) {
        this.ws.send(JSON.stringify({ type: 'HEARTBEAT' }));
      }
    }, 30000);
  }
}
```

### Message Handling
```typescript
class MessageHandler {
  private handlers: Map<string, Function> = new Map();

  registerHandler(type: string, handler: Function) {
    this.handlers.set(type, handler);
  }

  async handleMessage(event: MessageEvent) {
    const message = JSON.parse(event.data);
    const handler = this.handlers.get(message.type);
    
    if (handler) {
      try {
        await handler(message.data);
      } catch (error) {
        console.error(`Error handling message type ${message.type}:`, error);
      }
    }
  }
}
```

## Best Practices

### 1. Message Batching
```typescript
class MessageBatcher {
  private batch: Map<string, any[]> = new Map();
  private batchInterval = 100; // ms

  constructor() {
    setInterval(() => this.flush(), this.batchInterval);
  }

  addToBatch(type: string, data: any) {
    if (!this.batch.has(type)) {
      this.batch.set(type, []);
    }
    this.batch.get(type)!.push(data);
  }

  private flush() {
    this.batch.forEach((items, type) => {
      if (items.length > 0) {
        ws.send(JSON.stringify({
          type,
          data: items
        }));
        items.length = 0;
      }
    });
  }
}
```

### 2. Rate Limiting
```typescript
class RateLimiter {
  private messageCount: Map<string, number> = new Map();
  private readonly limit = 100; // messages per window
  private readonly window = 60000; // 1 minute

  checkLimit(userId: string): boolean {
    const count = this.messageCount.get(userId) || 0;
    if (count >= this.limit) return false;
    
    this.messageCount.set(userId, count + 1);
    setTimeout(() => {
      this.messageCount.set(userId, count);
    }, this.window);
    
    return true;
  }
}
```

### 3. State Management
```typescript
class StateManager {
  private state: Map<string, any> = new Map();
  private subscribers: Map<string, Set<Function>> = new Map();

  updateState(key: string, value: any) {
    this.state.set(key, value);
    this.notifySubscribers(key, value);
  }

  subscribe(key: string, callback: Function) {
    if (!this.subscribers.has(key)) {
      this.subscribers.set(key, new Set());
    }
    this.subscribers.get(key)!.add(callback);
  }

  private notifySubscribers(key: string, value: any) {
    this.subscribers.get(key)?.forEach(callback => callback(value));
  }
}
```

## Security Considerations

### 1. Message Validation
```typescript
function validateMessage(message: any): boolean {
  if (!message.type || !message.data) return false;
  if (typeof message.type !== 'string') return false;
  
  // Additional validation based on message type
  switch (message.type) {
    case 'VIRTUAL_TRADE':
      return validateTradeMessage(message.data);
    // Add other message type validations
  }
  
  return true;
}
```

### 2. Authentication
```typescript
async function authenticateConnection(
  request: WebSocket.ConnectRequest
): Promise<boolean> {
  const token = request.headers['authorization'];
  if (!token) return false;
  
  try {
    const decoded = await verifyToken(token);
    return true;
  } catch (error) {
    return false;
  }
}
```

## Testing

### 1. Mock WebSocket Server
```typescript
class MockWebSocketServer {
  private clients: Set<WebSocket> = new Set();

  broadcast(message: any) {
    this.clients.forEach(client => {
      if (client.readyState === WebSocket.OPEN) {
        client.send(JSON.stringify(message));
      }
    });
  }

  simulateMessage(message: any) {
    this.broadcast(message);
  }
}
```

### 2. Connection Testing
```typescript
describe('WebSocket Connection', () => {
  let mockServer: MockWebSocketServer;
  let client: WebSocketClient;

  beforeEach(() => {
    mockServer = new MockWebSocketServer();
    client = new WebSocketClient();
  });

  it('should handle reconnection', async () => {
    await client.connect();
    mockServer.close();
    // Assert reconnection attempt
  });
});
```
