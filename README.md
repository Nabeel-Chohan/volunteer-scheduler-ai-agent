# Volunteer Scheduler AI Agent (VSAI)

## Overview

The Volunteer Scheduler AI Agent (VSAI) is a lightweight, SMS-driven solution for managing volunteer schedules. It allows volunteers to interact with the scheduling system using natural language via SMS, eliminating the need for a dedicated app or login. The VSAI can understand volunteer availability, process cancellations, and assign shifts automatically.

For a detailed technical breakdown, please see the [**Architecture Document**](./ARCHITECTURE.md).

## How it Works

1.  **Volunteer Sends SMS:** A volunteer sends a text message to a dedicated number to indicate their availability or cancel a shift.
2.  **AI-Powered Intent Parsing:** The system uses the OpenAI API to understand the volunteer's intent and extract key details like dates and times.
3.  **Schedule Update:** The backend service, built with Python and FastAPI, updates the schedule in the database.
4.  **Automated Shift Filling:** If a shift is canceled, the system automatically finds the next best available volunteer and contacts them via SMS to fill the opening.
5.  **Confirmation SMS:** The volunteer receives an SMS to confirm their schedule changes.

## Technology Stack

This project uses a modern, open-source stack:

*   **Backend:** Python with [FastAPI](https://fastapi.tiangolo.com/)
*   **SMS Gateway:** [Twilio](https://www.twilio.com/sms)
*   **AI Intent Parsing:** [OpenAI API](https://openai.com/api/)
*   **Database:** [SQLite](https://www.sqlite.org/index.html) (for the MVP)
*   **Notifications:** [SendGrid](https://sendgrid.com/) (for staff alerts)
*   **Hosting:** [Render](https://render.com/) or any other platform that supports Python web applications.

## Getting Started

To get started with development, please refer to the detailed setup and workflow information in the [**Architecture Document**](./ARCHITECTURE.md).
