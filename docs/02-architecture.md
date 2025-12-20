# Architecture (Microsoft MVP)

## Core Components
1. Azure Communication Services (SMS)
2. Power Automate (workflow engine)
3. Azure OpenAI (intent parsing)
4. Microsoft Lists (volunteer + shift database)
5. Outlook Calendar (optional)
6. Microsoft Teams (optional staff notifications)

## Simple Architecture Diagram
Volunteer SMS
   ↓
Azure Communication Services (SMS)
   ↓
Power Automate Flow
   ↓
Azure OpenAI (intent extraction)
   ↓
Microsoft Lists (update records)
   ↓
Optional: Outlook Calendar + Teams notifications

## Why This Architecture
- No servers or custom backend
- Minimal moving parts
- Fully Microsoft ecosystem
- Easy to maintain and extend
