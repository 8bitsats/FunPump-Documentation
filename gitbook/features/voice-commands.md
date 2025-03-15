# Voice Commands

FunPump integrates a sophisticated voice interface that allows users to launch tokens and execute trades using natural language. This feature combines OpenAI's GPT-4, Deepgram's real-time speech recognition, and LiveKit's audio streaming capabilities.

## Overview

The voice interface provides a natural way to interact with FunPump:
- Launch new tokens verbally
- Execute trades through voice commands
- Get market insights and analytics
- Navigate virtual environments

## Components

### Speech Recognition
- Real-time transcription via Deepgram
- Low-latency audio processing
- Multi-language support
- Noise cancellation

### Natural Language Processing
- GPT-4 powered understanding
- Context-aware responses
- Intent recognition
- Parameter extraction

### Voice Response
- Text-to-speech using OpenAI TTS-1
- Natural-sounding feedback
- Dynamic response generation
- Error handling notifications

## Common Voice Commands

### Token Launch Commands
```
"Launch a new token called [name] with symbol [symbol]"
"Create a compressed token with [parameters]"
"Set initial price to [amount] SOL"
"Add [amount] SOL initial liquidity"
```

### Trading Commands
```
"Buy [amount] tokens at market price"
"Sell [amount] tokens"
"Show me the current price"
"What's the price impact for buying [amount]?"
```

### Virtual Environment Commands
```
"Create a new virtual environment"
"Join environment [name]"
"Show me the bonding curve"
"Simulate a trade of [amount] tokens"
```

### Information Commands
```
"What's my current reward tier?"
"Show my unclaimed rewards"
"What's the trading volume today?"
"Give me market insights for [token]"
```

## Voice Assistant Flow

1. **Initialization**
   ```typescript
   const voiceAssistant = new VoiceAssistant({
     deepgramApiKey: process.env.DEEPGRAM_API_KEY,
     openAiApiKey: process.env.OPENAI_API_KEY,
     liveKitUrl: process.env.LIVEKIT_URL
   });
   ```

2. **Command Processing**
   ```typescript
   voiceAssistant.onTranscript(async (transcript) => {
     const intent = await voiceAssistant.processIntent(transcript);
     const response = await voiceAssistant.executeCommand(intent);
     await voiceAssistant.speak(response);
   });
   ```

3. **Parameter Extraction**
   ```typescript
   interface TokenLaunchParams {
     name: string;
     symbol: string;
     initialPrice?: number;
     liquidity?: number;
     isCompressed?: boolean;
   }

   const params = await voiceAssistant.extractTokenParams(transcript);
   ```

## Error Handling

The voice interface includes robust error handling:

1. **Unclear Commands**
   - Request clarification
   - Provide examples
   - Suggest alternatives

2. **Network Issues**
   - Automatic reconnection
   - State preservation
   - Feedback to user

3. **Parameter Validation**
   - Confirm critical parameters
   - Validate values
   - Request corrections

## Best Practices

### Clear Speech
- Speak naturally but clearly
- Use defined command patterns
- Wait for confirmations
- Check feedback

### Environment
- Minimize background noise
- Use a good microphone
- Maintain stable connection
- Monitor voice feedback

### Command Structure
- Start with action verb
- Specify parameters clearly
- Use standard terminology
- Confirm important actions

## Examples

### Token Launch Example
```
User: "Launch a new token called Moon Rocket with symbol MOON"
Assistant: "I'll help you launch Moon Rocket token. What initial price would you like to set?"
User: "Set initial price to 0.1 SOL"
Assistant: "Got it. Would you like to add initial liquidity?"
User: "Yes, add 10 SOL liquidity"
Assistant: "I'll create MOON token with 0.1 SOL initial price and 10 SOL liquidity. Should I proceed?"
User: "Yes, launch it"
Assistant: "Launching Moon Rocket token... Transaction confirmed! Your token is now live."
```

### Trading Example
```
User: "What's the current price of MOON?"
Assistant: "MOON is currently trading at 0.15 SOL"
User: "Buy 100 MOON tokens"
Assistant: "The price impact for buying 100 MOON would be 2%. Would you like to proceed?"
User: "Yes, execute the trade"
Assistant: "Executing trade... Transaction confirmed! You've received 100 MOON tokens."
```

## Security Considerations

### Voice Authentication
- Wallet confirmation required
- Critical action verification
- Multi-factor for large trades
- Session management

### Privacy
- Local audio processing
- Encrypted transmission
- Data retention policies
- User consent management

## Troubleshooting

### Common Issues
1. **Recognition Problems**
   - Check microphone
   - Reduce background noise
   - Speak more clearly
   - Update browser

2. **Command Issues**
   - Review command structure
   - Check parameter values
   - Verify wallet connection
   - Confirm network status

3. **Connection Problems**
   - Check internet connection
   - Verify WebSocket status
   - Reconnect wallet
   - Clear browser cache

## Future Enhancements

Planned improvements include:
- Multi-language support
- Custom command creation
- Voice profile training
- Advanced market analysis
- Predictive suggestions
