# Calls

## Send Call

Send an AI phone call with a custom objective and actions.

### POST /v1/calls

#### Description

Send an AI phone call with a custom objective and actions.

#### Headers

| Name           | Type   | Required | Description                                            |
|----------------|--------|----------|--------------------------------------------------------|
| authorization  | string | required | Your API key for authentication.                       |
| encrypted_key  | string | optional | Special key for using a BYOT (Bring Your Own Twilio) account. Only required for sending calls from your own Twilio account. |

#### Body Parameters

| Name                | Type     | Required | Description                                                                                   |
|---------------------|----------|----------|-----------------------------------------------------------------------------------------------|
| phone_number        | string   | required | The phone number to call. Country code defaults to +1 (US) if not specified. For best results, use the E.164 format. Formatting Examples |
| task                | string   | required | Provide instructions, relevant information, and examples of the ideal conversation flow. Recommendations: Include context and a background/persona for the agent like "You are {name}, a customer service agent at {company} calling {name} about {reason}. Phrase instructions like you are speaking to the agent before the call. Any time you tell the agent not to do something, provide an example of what they should do instead. Keep the prompt under 2,000 characters where possible. |
| pathway_id          | string   | optional | The pathway ID for the pathway you have created on our dev portal. Certain parameters do not apply when using pathways. |
| start_node_id       | string   | optional | The node ID for the node you want the pathway to start from. Only used when pathway_id is provided. |
| voice               | string   | optional | The voice of the AI agent to use. Accepts any form of voice ID, including custom voice clones and voice presets. Default: "mason". Use the GET /v1/voices endpoint to see a full list of your available voices. |
| first_sentence      | string   | optional | Makes your agent say a specific phrase or sentence for its first response. |
| wait_for_greeting   | boolean  | optional | By default, the agent starts talking as soon as the call connects. When set to true, the agent will wait for the call recipient to speak first before responding. Default: false. |
| block_interruptions | boolean  | optional | When set to true, the AI will not respond or process interruptions from the user. Default: false. |
| interruption_threshold | number | optional | Adjusts how patient the AI is when waiting for the user to finish speaking. Lower values mean the AI will respond more quickly, while higher values mean the AI will wait longer before responding. Recommended range: 50-200. Default: 100. |
| model               | string   | optional | Select a model to use for your call. Options: base, turbo, and enhanced. Default: "enhanced". |
| temperature         | float    | optional | A value between 0 and 1 that controls the randomness of the LLM. 0 will cause more deterministic outputs while 1 will cause more random. Default: 0.7. Example Values: "0.9", "0.3", "0.5". |
| keywords            | string[] | optional | These words will be boosted in the transcription engine - recommended for proper nouns or words that are frequently mis-transcribed. For stronger keyword boosts, you can place a colon then a boost factor after the word. The default boost factor is 2. Example: {"keywords": ["Reece:3"]}. |
| pronunciation_guide | array    | optional | Guides the agent on how to say specific words. This is great for situations with complicated terms or names. Example: [{"word": "example", "pronunciation": "ex-am-ple", "case_sensitive": "false", "spaced": "false"}] |
| transfer_phone_number | string | optional | A phone number that the agent can transfer to under specific conditions - such as being asked to speak to a human or supervisor. |
| transfer_list       | object   | optional | Give your agent the ability to transfer calls to a set of phone numbers. Overrides transfer_phone_number if a transfer_list.default is specified. Example: {"transfer_list": {"default": "+12223334444", "sales": "+12223334444", "support": "+12223334444", "billing": "+12223334444"}} |
| language            | string   | optional | Select a supported language of your choice. Optimizes every part of our API for that language. Default: "en-US". |
| calendly            | object   | optional | To use Calendly integration you must provide the timezone to get availability/book in and the event url. Example: {"calendly": {"url": "CALENDLY_EVENT_URL", "timezone": "America/Chicago"}} |
| timezone            | string   | optional | Set the timezone for the call. Handled automatically for calls in the US. Default: "America/Los_Angeles". |
| request_data        | object   | optional | Any JSON you put in here will be visible to the AI agent during the call - and can also be referenced with Prompt Variables. Example: {"phone_number": "+1...", "task": "...", "first_sentence": "Hello {{name}}! How are you doing today?", "request_data": {"name": "John Doe"}} |
| tools               | array    | optional | Interact with the real world through API calls. |
| dynamic_data        | array    | optional | Make dynamic requests to external APIs and use the data in your AI’s responses. |
| start_time          | string   | optional | The time you want the call to start. If you don’t specify a time (or the time is in the past), the call will send immediately. Format: YYYY-MM-DD HH:MM:SS -HH:MM. Timezone is optional, defaults to UTC if not specified. |
| voicemail_message   | string   | optional | When the AI encounters a voicemail, it will leave this message after the beep and then immediately end the call. |
| voicemail_action    | enum     | optional | Options: hangup, leave_message, ignore. Default: "hangup". |
| max_duration        | integer  | optional | When the call starts, a timer is set for the max_duration minutes. At the end of that timer, if the call is still active it will be automatically ended. Default: 30. |
| record              | boolean  | optional | To record your phone call, set record to true. Default: false. |
| from                | string   | optional | Specify a phone number to call from that you own or have uploaded from your Twilio account. Country code is required, spaces or parentheses must be excluded. |
| webhook             | string   | optional | When the call ends, we’ll send the call details in a POST request to the URL you specify here. |
| metadata            | object   | optional | Add any additional information you want to associate with the call. Example: {"metadata": {"campaign_id": "1234", "source": "web"}} |
| summary_prompt      | string   | optional | At the end of each call, a summary is generated based on the transcript - you can use this field to add extra instructions and context for how it should be summarized. Example: "Summarize the call in French instead of English." |
| analysis_prompt     | string   | optional | Guides the output and provides additional instructions and clarifications for the analysis_schema. |
| analysis_schema     | object   | optional | Define a JSON schema for how you want to get information about the call - information like email addresses, names, appointment times, or any other type of custom data. Example: {"analysis_schema": {"email_address": "email", "first_name": "string", "last_name": "string", "wants_to_book_appointment": "boolean", "appointment_time": "YYYY-MM-DD HH:MM:SS"}} |
| answered_by_enabled | boolean  | optional | If set to true, processes the audio from the start of the call to determine if it was answered by a human or a voicemail. Default: false. |

#### Response

| Name      | Type   | Description                                                                    |
|-----------|--------|--------------------------------------------------------------------------------|
| status    | string | Can be success or error.                                                       |
| call_id   | string | A unique identifier for the call (present only if status is success).          |
| batch_id  | string | The batch ID of the call (present only if status is success).                  |
| message   | string | A message explaining the status of the call.                                   |
| errors    | array  | For validation errors, a detailed list of each field with an error and its message. Example: {"status": "error", "message": "Invalid parameters", "errors": ["Missing required parameter: phone_number.", "Missing required parameter: task.", "Phone number must be a string or number.", "Task must be a string."]} |

#### CURL Example

```shell
curl --request POST \
  --url https://api.bland.ai/v1/calls \
  --header 'Content-Type: application/json' \
  --header 'authorization: <authorization>' \
  --data '{
  "phone_number": "<string>",
  "task": "<string>",
  "pathway_id": "<string>",
  "start_node_id": "<string>",
  "voice": "<string>",
  "first_sentence": "<string>",
  "wait_for_greeting": true,
  "block_interruptions": true,
  "interruption_threshold": 123,
  "model": "<string>",
  "temperature": 123,
  "keywords": [
    "<string>"
  ],
  "pronunciation_guide": [
    {}
  ],
  "transfer_phone_number": "<string>",
  "transfer_list": {},
  "language": "<string>",
  "calendly": {},
  "timezone": "<string>",
  "request_data": {},
  "tools": [
    {}
  ],
  "dynamic_data": [
    {
      "dynamic_data[i].response_data": [
        {}
      ]
    }
  ],
  "start_time": "<string>",
  "voicemail_message": "<string>",
  "voicemail_action": {},
  "max_duration": 123,
  "record": true,
  "from": "<string>",
  "webhook": "<string>",
  "metadata": {},
  "summary_prompt": "<string>",
  "analysis_prompt": "<string>",
  "analysis_schema": {},
  "answered_by_enabled": true
}'
```

#### Interactive Playground

For an interactive API playground, you can use the [Swagger UI](https://swagger.io/tools/swagger-ui/) or [Redocly](https://redocly.com/) to test and interact with the API endpoints. 

1. **Swagger UI**:
   - Add the OpenAPI specification JSON/YAML file to the Swagger UI.
   - Example: 
     ```yaml
     openapi: 3.0.0
     info:
       title: Calls API
       description: API to send AI phone calls with custom objectives and actions.
       version: 1.0.0
     paths:
       /v1/calls:
         post:
           summary: Send Call
           description: Send an AI phone call with a custom objective and actions.
           requestBody:
             required: true
             content:
               application/json:
                 schema:
                   type: object
                   properties:
                     phone_number:
                       type: string
                       description: The phone number to call.
                       example: "+1975934749"
                     task:
                       type: string
                       description: Provide instructions, relevant information, and examples of the ideal conversation flow.
                       example: "You are a customer service agent at XYZ company calling John Doe about a recent order."
                   required:
                     - phone_number
                     - task
           responses:
             '200':
               description: Successful response
               content:
                 application/json:
                   schema:
                     type: object
                     properties:
                       status:
                         type: string
                         example: success
                       call_id:
                         type: string
                         example: "9d404c1b-6a23-4426-953a-a52c392ff8f1"
                     required:
                       - status
                       - call_id
             '400':
               description: Invalid parameters
               content:
                 application/json:
                   schema:
                     type: object
                     properties:
                       status:
                         type: string
                         example: error
                       message:
                         type: string
                         example: Invalid parameters
                       errors:
                         type: array
                         items:
                           type: string
                         example: ["Missing required parameter: phone_number.", "Missing required parameter: task."]
                     required:
                       - status
                       - message
                       - errors
     ```

2. **Redocly**:
   - Add the OpenAPI specification JSON/YAML file to Redocly.
   - Example: Similar to Swagger UI, create an OpenAPI specification and integrate it with Redocly for an interactive documentation experience.

You can embed these tools directly into your Mintlify documentation or provide links to the hosted Swagger UI/Redocly pages.
