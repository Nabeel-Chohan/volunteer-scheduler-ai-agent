# Workflow ("Recipe")

## 1. Volunteer Sends SMS
Examples:
- "I can help Saturday morning"
- "I need to cancel my shift"
- "I'm available afternoons"

## 2. Power Automate Receives SMS
Trigger: ACS → Power Automate

## 3. Power Automate Calls Azure OpenAI
Prompt instructs AI to extract:
- Intent (availability, cancellation, update)
- Date and time
- Volunteer identity
- Skills (if mentioned)

## 4. Update Microsoft Lists
- Update volunteer availability
- Mark shifts as filled or open
- Log cancellations

## 5. Self-Healing Roster Logic
If a shift becomes open:
- Identify next best volunteer (skills + availability)
- Send SMS asking if they can fill the shift
- Wait for Yes/No
- Update Lists accordingly

## 6. Send Confirmation SMS
Examples:
- "You're confirmed for Saturday 9–11am"
- "Thanks for the update, your shift is cancelled"

## 7. Optional Staff Notifications
- Post to Teams channel
- Update Outlook Calendar
