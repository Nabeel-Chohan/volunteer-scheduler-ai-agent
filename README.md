# Volunteer Scheduler AI Agent (VSAI)

## Overview

The Volunteer Scheduler AI Agent (VSAI) is a lightweight, SMS-driven solution for managing volunteer schedules. It allows volunteers to interact with the scheduling system using natural language via SMS, eliminating the need for a dedicated app or login. The VSAI can understand volunteer availability, process cancellations, and assign shifts, all while keeping the schedule updated in a Microsoft List and notifying staff as needed.

## How it Works

1.  **Volunteer Sends SMS:** A volunteer sends a text message to the dedicated phone number (e.g., "I can help Saturday morning," "I need to cancel my shift," "I'm available afternoons").
2.  **Power Automate Receives SMS:** The message is received by Azure Communication Services and triggers a Power Automate workflow.
3.  **AI-Powered Intent Parsing:** The Power Automate flow sends the message to Azure OpenAI, which extracts the volunteer's intent (e.g., availability, cancellation), along with relevant details like dates and times.
4.  **Schedule Update:** The extracted information is used to update the volunteer and shift schedules in Microsoft Lists.
5.  **Automated Shift Filling:** If a shift becomes open due to a cancellation, the system identifies the next best available volunteer and sends them an SMS to ask if they can fill the shift.
6.  **Confirmation SMS:** The volunteer receives a confirmation SMS (e.g., "You're confirmed for Saturday 9–11am," "Thanks for the update, your shift is cancelled").
7.  **Staff Notifications (Optional):** Staff can be notified of schedule changes via Microsoft Teams or an updated Outlook Calendar.

## Architecture

The VSAI is built entirely on the Microsoft ecosystem, using a serverless architecture to minimize complexity and cost.

*   **Volunteer SMS**
*   **Azure Communication Services (SMS)**
*   **Power Automate Flow**
*   **Azure OpenAI (intent extraction)**
*   **Microsoft Lists (update records)**
*   **Optional: Outlook Calendar + Teams notifications**

## Technologies Used

### Required
- **Azure Communication Services (SMS):** Handles inbound and outbound SMS messages.
- **Power Automate:** Orchestrates the entire workflow, connecting all the services.
- **Azure OpenAI:** Parses incoming SMS messages to understand volunteer intent.
- **Microsoft Lists:** Serves as the database for volunteer and shift information.

### Optional
- **Outlook Calendar:** Provides a visual schedule for staff.
- **Microsoft Teams:** Used for staff notifications.
- **Azure Key Vault:** Recommended for secure storage of API keys.

## Getting Started

To get started with the VSAI, you will need to provision the required Azure services and configure the Power Automate flow. Detailed setup instructions will be provided in the project documentation.

## MVP Scope

### In-Scope
- SMS intake
- AI intent parsing
- Basic volunteer database
- Basic shift database
- Simple matching logic
- Confirmation loop
- Staff notifications (optional)

### Out-of-Scope (for now)
- Mobile app
- Web dashboard
- Complex skill weighting
- Multi-language support
- Automated fairness balancing
- Advanced analytics

The primary success metric for the MVP is the ability to automatically fill a cancelled shift within 5 minutes, without human intervention.
