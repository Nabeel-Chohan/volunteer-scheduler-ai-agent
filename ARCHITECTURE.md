# Volunteer Scheduler AI Agent (VSAI) - Open-Source MVP Architecture

## 1. Introduction

The following document outlines a robust, scalable, and cost-effective open-source architecture for the Volunteer Scheduler AI Agent (VSAI) MVP. This proposed architecture provides a developer-friendly alternative to the Microsoft-centric model detailed in the project's `README.md`. By leveraging a popular Python-based stack and best-in-class open-source tools, this architecture is designed for rapid development, easy deployment, and low-cost maintenance, all while harnessing the power of the OpenAI ecosystem for advanced natural language understanding.

## 2. High-Level Architecture

The proposed system is composed of several key components that work together to deliver the core functionality of the VSAI. The diagram below illustrates the high-level data flow:

```
+-----------------+      +-------------------+      +--------------------------+
| Volunteer SMS   |----->|  Twilio (SMS GW)  |----->| FastAPI Backend (Python) |
+-----------------+      +-------------------+      +--------------------------+
                                                              |
                                                              |
                                               +--------------+--------------+
                                               |                             |
                                               v                             v
                                     +------------------+      +------------------------+
                                     | OpenAI API       |      | SQLite/PostgreSQL (DB) |
                                     +------------------+      +------------------------+
                                               ^                             ^
                                               |                             |
                                               +--------------+--------------+
                                                              |
                                                              v
                                                     +-----------------+
                                                     | SendGrid (Email)|----->| Staff Notification |
                                                     +-----------------+      +--------------------+

```

**Workflow Overview:**

1.  A volunteer sends an SMS message to a dedicated phone number provisioned through **Twilio**.
2.  Twilio receives the SMS and forwards it to a webhook endpoint exposed by the **FastAPI backend service**.
3.  The FastAPI application receives the SMS content and orchestrates the required actions.
4.  The backend sends the SMS text to the **OpenAI API** to parse the volunteer's intent (e.g., availability, cancellation) and extract key entities like dates and times.
5.  Based on the parsed intent, the backend interacts with the **database** (SQLite for the MVP) to update volunteer records and shift schedules.
6.  If a shift is canceled, the backend service queries the database for the next available volunteer and sends them an SMS via the Twilio API to ask if they can fill the shift.
7.  For other actions, such as confirming availability, the backend sends a confirmation SMS to the original volunteer via Twilio.
8.  Optionally, staff can be notified of important events (e.g., a canceled shift that could not be filled automatically) via email, using the **SendGrid API**.

The entire backend service is designed to be containerized and hosted on a platform like **Render** or **Fly.io**, both of which offer generous free tiers suitable for an MVP.

## 3. Component Breakdown and Technology Choices

This section provides a detailed breakdown of each component in the proposed open-source architecture for the VSAI, with a justification for each technology choice.

**3.1. SMS Gateway: Twilio**

*   **Technology:** [Twilio Programmable SMS](https://www.twilio.com/sms)
*   **Role:** To handle all inbound and outbound SMS communications with volunteers.
*   **Justification:**
    *   **Developer-Friendly:** Twilio is renowned for its excellent API documentation and robust Python client library, which will significantly speed up development.
    *   **Cost-Effective for MVP:** Twilio offers a free trial with enough credits to build and test the MVP thoroughly.
    *   **Scalability:** Twilio's infrastructure is built to scale, so as the VSAI grows, it can handle increased message volume without any architectural changes.

**3.2. Backend Service: Python with FastAPI**

*   **Technology:** [Python](https://www.python.org/) and the [FastAPI](https://fastapi.tiangolo.com/) web framework.
*   **Role:** The core of the application, this service will expose a webhook for Twilio, process incoming messages, orchestrate calls to the OpenAI API, and interact with the database.
*   **Justification:**
    *   **Performance:** FastAPI is a high-performance web framework for Python, capable of handling a large number of requests efficiently.
    *   **Ease of Use:** FastAPI's modern features and type-hinting system make it easy to write clean, maintainable, and well-documented code. It also provides automatic interactive API documentation.
    *   **Asynchronous by Design:** FastAPI is built for asynchronous programming, which is ideal for an application that needs to make external API calls (to OpenAI and Twilio) without blocking.

**3.3. Database: SQLite (with an upgrade path to PostgreSQL)**

*   **Technology:** [SQLite](https://www.sqlite.org/index.html) for the MVP, with a recommendation to use an ORM like [SQLAlchemy](https://www.sqlalchemy.org/) for a seamless transition to [PostgreSQL](https://www.postgresql.org/).
*   **Role:** To store all persistent data, including volunteer information and shift schedules.
*   **Justification:**
    *   **Simplicity for MVP:** SQLite is a serverless, file-based database that requires no setup, making it perfect for rapid prototyping and the initial MVP.
    *   **Cost-Free:** SQLite is completely free and open-source.
    *   **Scalability Path:** By using an ORM like SQLAlchemy from the start, the application can be easily migrated to a more robust database like PostgreSQL when the need for more advanced features and concurrent access arises.

**3.4. AI Intent Parsing: OpenAI API**

*   **Technology:** [OpenAI API](https://openai.com/api/), specifically the `gpt-3.5-turbo` model.
*   **Role:** To parse the natural language from incoming SMS messages and extract the volunteer's intent and any relevant entities (like dates, times, and specific requests).
*   **Justification:**
    *   **State-of-the-Art NLP:** The OpenAI API provides access to powerful language models that can accurately understand and interpret a wide range of volunteer messages.
    *   **Cost-Effective Model:** The `gpt-3.5-turbo` model provides an excellent balance of performance and cost, making it ideal for the MVP.
    *   **Flexibility:** A well-designed prompt will allow the system to handle various phrasings and requests without needing complex, rule-based logic.

**3.5. Notifications: SendGrid**

*   **Technology:** [Twilio SendGrid](https://sendgrid.com/)
*   **Role:** To send email notifications to staff when there are important updates, such as a shift being canceled and not automatically filled.
*   **Justification:**
    *   **Generous Free Tier:** SendGrid's free plan allows for up to 100 emails per day, which is more than sufficient for the MVP's notification needs.
    *   **Reliability:** SendGrid is a trusted name in email delivery, ensuring that important notifications reach the staff.
    *   **Simple Integration:** It offers a straightforward API and a Python client library for easy integration.

**3.6. Hosting: Render**

*   **Technology:** [Render](https://render.com/)
*   **Role:** To host the FastAPI backend service and the database.
*   **Justification:**
    *   **Generous Free Tier:** Render offers a free tier for web services that is perfect for an MVP. It's also possible to host a small PostgreSQL instance for free, making it a great all-in-one solution.
    *   **Git-Based Deployments:** Render integrates directly with GitHub, allowing for automatic deployments on every `git push`.
    *   **Ease of Scaling:** As the VSAI grows, you can easily upgrade to a paid plan on Render without needing to change the core architecture.

## 4. Detailed Workflow

This section outlines the step-by-step data flow for the two primary use cases of the VSAI system.

**4.1. Flow 1: Volunteer Expresses Availability or Cancels a Shift**

This workflow is initiated when a volunteer sends an SMS to the system.

1.  **Volunteer Sends SMS:** The volunteer sends a text message to the Twilio phone number.
    *   *Example:* "Hi, I can work this Saturday morning." or "I'm so sorry, but I need to cancel my shift on Tuesday."

2.  **Twilio Receives and Forwards SMS:** Twilio receives the SMS and makes an HTTP POST request to the `/sms-webhook` endpoint of the FastAPI backend. The request body contains the sender's phone number and the message content.

3.  **Backend Receives Webhook:** The FastAPI service receives the POST request and extracts the `From` (phone number) and `Body` (message content) from the payload.

4.  **Backend Queries Database for Volunteer:** The service queries the `volunteers` table in the SQLite database using the sender's phone number to identify the volunteer. If the volunteer is not found, a new entry can be created.

5.  **Backend Calls OpenAI for Intent Parsing:** The backend constructs a prompt for the OpenAI API, including the SMS message content. It sends a request to the `gpt-3.5-turbo` model to extract the intent and any relevant entities.
    *   **Prompt Example:** See the "OpenAI Integration" section below for a detailed example.
    *   **OpenAI Response (JSON):**
        ```json
        {
          "intent": "SET_AVAILABILITY",
          "entities": {
            "date": "2024-10-26",
            "time_of_day": "morning"
          }
        }
        ```
        or
        ```json
        {
          "intent": "CANCEL_SHIFT",
          "entities": {
            "date": "2024-10-22"
          }
        }
        ```

6.  **Backend Processes Intent:**
    *   **If `intent` is `SET_AVAILABILITY`:** The service updates the `volunteers` table to record the new availability for the identified volunteer.
    *   **If `intent` is `CANCEL_SHIFT`:** The service finds the corresponding shift in the `shifts` table for that volunteer and date, marks it as "open," and sets the `volunteer_id` to `NULL`.

7.  **Backend Sends Confirmation SMS:** The backend uses the Twilio client library to send a confirmation SMS back to the volunteer.
    *   *Example:* "Thanks! We've marked you as available for this coming Saturday morning." or "Your shift for Tuesday has been canceled. Thanks for letting us know."

8.  **Trigger Automated Shift Filling (if applicable):** If the intent was `CANCEL_SHIFT`, the backend now initiates the "Automated Shift Filling" workflow (see Flow 2).

**4.2. Flow 2: Automated Shift Filling**

This workflow is triggered automatically after a shift is canceled.

1.  **Identify Canceled Shift:** The system has just processed a cancellation and has the details of the now-open shift (e.g., date, time, required skills).

2.  **Find Available Volunteers:** The backend queries the `volunteers` table to find a list of volunteers who are marked as available for the date and time of the open shift. The query should exclude the volunteer who just canceled and any volunteers who have already been contacted for this shift.
    *   **Logic:** The query can be ordered by a "last_contacted" timestamp or a "shifts_worked_this_month" counter to ensure fairness in how often volunteers are contacted.

3.  **Select Best Volunteer and Send SMS:** The service selects the top volunteer from the list and sends them an SMS via the Twilio API.
    *   *Example:* "Hi Jane. A shift has opened up for this Tuesday from 10am to 1pm. Are you available to fill in? Please reply YES or NO."

4.  **Record Contact Attempt:** The backend updates the database to log that this volunteer has been contacted for this specific shift to avoid contacting them again for the same opening.

5.  **Handle Volunteer's Response:**
    *   **If the volunteer replies "YES":** The system receives the SMS via the same `/sms-webhook` endpoint. The OpenAI API can be used to parse the "YES" intent. The backend then updates the `shifts` table, assigning the shift to this volunteer, and sends a final confirmation SMS.
        *   *Example:* "Great, you're all set for the Tuesday shift! Thanks for your help."
    *   **If the volunteer replies "NO" or does not reply within a set time (e.g., 1 hour):** The backend selects the *next* available volunteer from the list and repeats step 3.
    *   **If all available volunteers have been contacted and no one accepts:** The system sends an email notification to the staff via SendGrid, informing them that the shift needs to be filled manually.
        *   *Example Email Subject:* "Urgent: Shift on Tuesday needs to be filled."

## 5. Database Schema and API Specifications

This section provides the proposed database schema for the MVP and the specifications for the primary API endpoint. The schema is designed to be simple for the MVP but extensible for future needs.

**5.1. Database Schema (SQLite)**

For the MVP, a simple SQLite database with two core tables is sufficient. Using an ORM like **SQLAlchemy** is highly recommended to abstract the SQL queries and facilitate a seamless future migration to a more robust database like PostgreSQL.

**Table: `volunteers`**

This table stores information about each volunteer.

| Column Name | Data Type | Description |
| :--- | :--- | :--- |
| `id` | INTEGER | Primary Key, Auto-incrementing. |
| `phone_number` | TEXT | Unique. The volunteer's mobile number in E.164 format (e.g., "+15551234567"). |
| `name` | TEXT | The volunteer's full name (can be populated later). |
| `created_at` | TIMESTAMP | The timestamp when the volunteer record was created. Defaults to the current time. |
| `availability` | TEXT | A JSON field to store general availability patterns (e.g., `{"day_of_week": "saturday", "time_of_day": "morning"}`). |

**Table: `shifts`**

This table stores information about each scheduled shift.

| Column Name | Data Type | Description |
| :--- | :--- | :--- |
| `id` | INTEGER | Primary Key, Auto-incrementing. |
| `volunteer_id`| INTEGER | Foreign Key referencing `volunteers.id`. Can be `NULL` if the shift is open. |
| `start_time` | TIMESTAMP | The specific start date and time of the shift. |
| `end_time` | TIMESTAMP | The specific end date and time of the shift. |
| `status` | TEXT | The current status of the shift (e.g., "confirmed", "open"). |
| `created_at` | TIMESTAMP | The timestamp when the shift was created. Defaults to the current time. |

**5.2. API Specifications**

The FastAPI backend will expose a single primary endpoint to serve as a webhook for receiving SMS messages from Twilio.

**Endpoint: `/sms-webhook`**

*   **Method:** `POST`
*   **Description:** This webhook is the main entry point for all volunteer interactions. Twilio will send an HTTP POST request to this endpoint every time a new SMS is received.
*   **Request Body (Content-Type: `application/x-www-form-urlencoded`):**
    *   `From`: The phone number of the volunteer who sent the SMS.
    *   `Body`: The text content of the SMS message.
    *   `MessageSid`: A unique identifier for the message, useful for logging and debugging.
*   **Success Response:**
    *   **Status Code:** `200 OK`
    *   **Content-Type:** `text/xml`
    *   **Body:** To acknowledge receipt and prevent Twilio from sending error notifications, the endpoint should respond with an empty TwiML `<Response/>` tag.
        ```xml
        <?xml version="1.0" encoding="UTF-8"?>
        <Response></Response>
        ```
    *   *Note:* All SMS replies to the volunteer will be sent asynchronously using the Twilio API, not in the direct response to the webhook. This prevents timeouts and allows for more complex processing.
*   **Error Response:**
    *   **Status Code:** `500 Internal Server Error`
    *   **Body:** A JSON object with an `error` key.
        ```json
        {"error": "Failed to process the incoming message."}
        ```

## 6. OpenAI Integration

The core of the VSAI's intelligence lies in its ability to understand natural language messages from volunteers. This is achieved by making a structured API call to the OpenAI `gpt-3.5-turbo` model. The key to getting accurate and consistent results is a well-designed prompt.

**6.1. Prompt Design**

The prompt should provide the model with clear instructions, context about its role, the desired output format, and the specific text to analyze. We will use the "system" and "user" message roles to guide the model's behavior.

**System Message:**

The system message sets the stage for the AI, telling it how to behave.

```
You are an expert intent parsing assistant for a volunteer scheduling system.
Your task is to analyze an SMS message from a volunteer and extract their intent and any relevant entities.
The possible intents are: SET_AVAILABILITY, CANCEL_SHIFT, AFFIRM, and NEGATE.
The entities to extract are: date (in YYYY-MM-DD format), time_of_day (morning, afternoon, or evening), and day_of_week.
The current date is {{current_date}}.
Always respond with a single, valid JSON object and nothing else.
```

**User Message:**

The user message will contain the actual SMS text from the volunteer.

```
Please analyze the following SMS message: "{{sms_message}}"
```

**6.2. Example API Call (Python)**

Here is a conceptual example of how to make the API call using the `openai` Python library.

```python
import openai
from datetime import datetime

# It is recommended to set the API key as an environment variable
# openai.api_key = "YOUR_OPENAI_API_KEY"

def parse_volunteer_intent(sms_text):
    """
    Analyzes the volunteer's SMS to extract intent and entities.
    """
    current_date = datetime.now().strftime("%Y-%m-%d")

    system_prompt = f"""
    You are an expert intent parsing assistant for a volunteer scheduling system.
    Your task is to analyze an SMS message from a volunteer and extract their intent and any relevant entities.
    The possible intents are: SET_AVAILABILITY, CANCEL_SHIFT, AFFIRM, and NEGATE.
    The entities to extract are: date (in YYYY-MM-DD format), time_of_day (morning, afternoon, or evening), and day_of_week.
    The current date is {current_date}.
    Always respond with a single, valid JSON object and nothing else.
    """

    user_prompt = f"Please analyze the following SMS message: \"{sms_text}\""

    try:
        response = openai.chat.completions.create(
            model="gpt-3.5-turbo",
            messages=[
                {"role": "system", "content": system_prompt},
                {"role": "user", "content": user_prompt}
            ],
            temperature=0.0 # Set to 0 for deterministic output
        )

        # The response from the API will be a JSON string
        return response.choices[0].message.content

    except Exception as e:
        print(f"An error occurred: {e}")
        return None

# --- Example Usage ---

# Example 1: Setting availability
sms_1 = "I'm free to help out this Saturday morning."
intent_1 = parse_volunteer_intent(sms_1)
print(intent_1)
# Expected Output:
# {
#   "intent": "SET_AVAILABILITY",
#   "entities": {
#     "date": "2024-10-26",  // Assuming today is before this Saturday
#     "time_of_day": "morning",
#     "day_of_week": "saturday"
#   }
# }


# Example 2: Canceling a shift
sms_2 = "I'm so sorry, I won't be able to make my Tuesday shift."
intent_2 = parse_volunteer_intent(sms_2)
print(intent_2)
# Expected Output:
# {
#   "intent": "CANCEL_SHIFT",
#   "entities": {
#     "date": "2024-10-22", // Assuming today is before this Tuesday
#     "time_of_day": null,
#     "day_of_week": "tuesday"
#   }
# }

# Example 3: Affirmative response to a shift offer
sms_3 = "Yes, I can do it."
intent_3 = parse_volunteer_intent(sms_3)
print(intent_3)
# Expected Output:
# {
#   "intent": "AFFIRM",
#   "entities": {}
# }

```

**6.3. Important Considerations**

*   **Date Resolution:** Providing the `current_date` in the system prompt is crucial. It gives the model the context it needs to correctly resolve relative dates like "this Saturday" or "next Tuesday" into a specific `YYYY-MM-DD` format.
*   **JSON Output:** Explicitly instructing the model to "Always respond with a single, valid JSON object" is key to getting structured data that can be easily parsed by the application.
*   **Error Handling:** The application code should include robust error handling to manage cases where the OpenAI API is unavailable or returns an unexpected response.
