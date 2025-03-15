# Voice Commands

FunPump's voice integration enables natural language interactions for token launches and trading. Powered by OpenAI, Deepgram, and LiveKit, it provides an intuitive way to interact with the platform.

## Core Components

- Deepgram for real-time speech recognition
- OpenAI GPT-4 for natural language understanding
- Text-to-speech using OpenAI TTS-1
- LiveKit for audio streaming

## Voice Assistant Features

- Token launch guidance
- Trading execution
- Market analysis
- Price checks
- Portfolio management

## Command Categories

- Launch Commands
- Trading Commands
- Analysis Commands
- System Commands

## Launch Commands

```typescript
// Example: Launch a new token
Assistant: "What would you like to name your token?"
User: "Moon Rocket"
Assistant: "What symbol should we use?"
User: "MOON"
```

## Trading Commands

```typescript
// Example: Buy tokens
Assistant: "How many tokens would you like to buy?"
User: "100 MOON tokens"
Assistant: "Current price is 0.1 SOL per token. Would you like to proceed?"
User: "Yes, execute the trade"
```

## Analysis Commands

```typescript
// Example: Price check
Assistant: "The current price of MOON is 0.15 SOL"
User: "What's the 24-hour volume?"
Assistant: "The 24-hour trading volume for MOON is 5000 SOL"
```

## System Commands

```typescript
// Example: Help request
User: "What commands are available?"
Assistant: "I can help you with:
1. Token launches ('launch a token')
2. Trading ('buy', 'sell')
3. Price checks ('what's the price of')
4. Market analysis ('show volume')"
```

## Integration Example

```typescript
const voiceAssistant = new VoiceAssistant({
  deepgramKey: process.env.DEEPGRAM_KEY,
  openaiKey: process.env.OPENAI_KEY,
  livekitToken: process.env.LIVEKIT_TOKEN
});

// Start voice session
await voiceAssistant.start();

// Handle commands
voiceAssistant.onCommand(async (command) => {
  switch (command.intent) {
    case 'LAUNCH_TOKEN':
      await handleTokenLaunch(command.parameters);
      break;
    case 'EXECUTE_TRADE':
      await handleTrade(command.parameters);
      break;
  }
});
```

## Voice Processing Pipeline

1. Audio capture using browser APIs
2. Real-time streaming to Deepgram
3. Text processing with GPT-4
4. Command execution
5. Response generation
6. Text-to-speech playback

## Setup Requirements

- Modern web browser
- Microphone access
- Stable internet connection
- Connected Solana wallet

## Best Practices

1. Speak clearly and naturally
2. Wait for assistant confirmation
3. Review commands before execution
4. Test in virtual environment first

## Error Handling

```typescript
voiceAssistant.onError((error) => {
  switch (error.type) {
    case 'SPEECH_RECOGNITION':
      // Handle speech recognition errors
      break;
    case 'COMMAND_PROCESSING':
      // Handle command processing errors
      break;
    case 'EXECUTION':
      // Handle execution errors
      break;
  }
});
```

## Support and Resources

- [Voice Command Guide](https://docs.funpump.ai/voice-commands)
- [API Reference](https://api.funpump.ai/voice)
- [Discord Support](https://discord.funpump.ai)
- [Video Tutorials](https://learn.funpump.ai/voice)

## Security Notes

1. Voice authentication is supplementary
2. Critical actions require wallet signature
3. Voice data is not stored permanently
4. All communication is encrypted

## Rate Limits

1. Maximum command length: 10 seconds
2. Commands per minute: 20
3. Concurrent sessions: 1
4. Daily usage limit: 1000 commands

## Troubleshooting

1. Check microphone permissions
2. Verify internet connection
3. Ensure wallet is connected
4. Clear browser cache if needed

For additional support, visit [help.funpump.ai](https://help.funpump.ai)
