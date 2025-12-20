# Tools ("Ingredients")

## Required
- **Azure Communication Services (SMS)**
  - Dedicated phone number
  - Handles inbound/outbound SMS

- **Power Automate**
  - Orchestrates all logic
  - Connects SMS → AI → Lists → Calendar

- **Azure OpenAI**
  - Parses volunteer messages
  - Extracts intent, dates, times, skills

- **Microsoft Lists**
  - Volunteer database
  - Shift schedule database

## Optional Enhancements
- **Outlook Calendar**
  - Visual schedule for staff

- **Microsoft Teams**
  - Staff notifications

- **Azure Key Vault**
  - Secure storage for API keys (recommended)
