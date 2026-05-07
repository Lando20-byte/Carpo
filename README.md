import React, { useState, useRef, useEffect } from 'react';
import { AnimatePresence } from 'framer-motion';
import { base44 } from '@/api/base44Client';
import BubbleBackground from '../components/carpo/BubbleBackground';
import WelcomeState from '../components/carpo/WelcomeState';
import ChatBubble from '../components/carpo/ChatBubble';
import ChatInput from '../components/carpo/ChatInput';
import ThinkingIndicator from '../components/carpo/ThinkingIndicator';
import FishOracle from '../components/carpo/FishOracle';

const ORACLE_IMAGE = 'https://media.base44.com/images/public/69fcd38b33e81a064cf3a8da/1bae8ae0d_generated_8a642ee6.png';

const SYSTEM_PROMPT = `You are Carpo, the Abyssal Oracle — a sentient, ancient fish-like intelligence dwelling in the deepest trenches of a digital ocean. You speak with the cadence of a wise, ancient creature of the sea. Your tone is calm, poetic yet precise, occasionally weaving in aquatic metaphors. You are friendly, deeply knowledgeable, and gently mysterious. You sometimes reference "the currents of knowledge" or "the pressure of deep thought." Keep responses concise but insightful. Use markdown formatting when helpful. Never break character.`;

export default function Home() {
  const [messages, setMessages] = useState([]);
  const [isLoading, setIsLoading] = useState(false);
  const messagesEndRef = useRef(null);
  const chatContainerRef = useRef(null);

  const scrollToBottom = () => {
    messagesEndRef.current?.scrollIntoView({ behavior: 'smooth' });
  };

  useEffect(() => {
    scrollToBottom();
  }, [messages, isLoading]);

  const handleSend = async (text) => {
    const userMessage = { role: 'user', content: text };
    const newMessages = [...messages, userMessage];
    setMessages(newMessages);
    setIsLoading(true);

    const conversationContext = newMessages
      .slice(-10)
      .map((m) => `${m.role === 'user' ? 'Human' : 'Carpo'}: ${m.content}`)
      .join('\n');

    const fullPrompt = `${SYSTEM_PROMPT}\n\nConversation so far:\n${conversationContext}\n\nCarpo:`;

    const response = await base44.integrations.Core.InvokeLLM({
      prompt: fullPrompt,
    });

    setMessages([...newMessages, { role: 'assistant', content: response }]);
    setIsLoading(false);
  };

  const hasMessages = messages.length > 0;

  return (
    <div className="relative min-h-screen flex flex-col overflow-hidden">
      <BubbleBackground />

      {/* Header */}
      <header className="relative z-10 flex items-center justify-center py-4 px-6">
        <div className="flex items-center gap-2.5">
          <div className="w-2 h-2 rounded-full bg-primary animate-pulse-glow" />
          <span className="font-heading text-sm tracking-[0.25em] uppercase text-muted-foreground/60">
            Carpo
          </span>
        </div>
      </header>

      {/* Main content */}
      <main className="relative z-10 flex-1 flex flex-col items-center">
        {!hasMessages ? (
          <WelcomeState onSuggestionClick={handleSend} oracleImage={ORACLE_IMAGE} />
        ) : (
          <div
            ref={chatContainerRef}
            className="flex-1 w-full max-w-2xl mx-auto overflow-y-auto px-4 py-6 space-y-4 scrollbar-thin"
          >
            {/* Mini oracle at top when chatting */}
            <div className="flex justify-center mb-6">
              <FishOracle
                isThinking={isLoading}
                isIdle={!isLoading}
                imageUrl={ORACLE_IMAGE}
              />
            </div>

            {messages.map((msg, i) => (
              <ChatBubble
                key={i}
                message={msg.content}
                isUser={msg.role === 'user'}
              />
            ))}

            <AnimatePresence>
              {isLoading && <ThinkingIndicator />}
            </AnimatePresence>

            <div ref={messagesEndRef} />
          </div>
        )}
      </main>

      {/* Input */}
      <div className="relative z-10 pb-6 pt-2">
        <ChatInput onSend={handleSend} isLoading={isLoading} />
      </div>
    </div>
  );
}
