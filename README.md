# Salesforce Voice Assistant for AgentForce

A Lightning Web Component (LWC) that enables natural voice interaction with Salesforce data through AgentForce, leveraging OpenAI's Whisper for speech-to-text and TTS for synthesized voice responses.

## Overview

This component creates a seamless voice interface in Salesforce:

1. **Speech-to-Text**: Captures user's voice through the microphone and transcribes it using OpenAI's Whisper API
2. **AgentForce Integration**: Sends the transcribed text to AgentForce for intelligent processing
3. **Text-to-Speech**: Converts AgentForce's response back to speech using OpenAI's TTS API

<img width="1642" alt="Capture d’écran 2025-04-23 à 11 08 19" src="https://github.com/user-attachments/assets/296b0b2c-1e6a-4a7a-85c4-4af7b97d7341" />


## Prerequisites

Before deploying this component, ensure you have:

- An active Salesforce org with API access
- OpenAI API key with credit balance
- AgentForce configured in your org
- Salesforce CLI (v2) installed locally
- The following information ready:
  - OpenAI API Key
  - Your Salesforce Org URL (e.g., `storm-41153b85fca0d8.my.salesforce.com`)
  - Client ID and Client Secret for OAuth
  - AgentForce Agent ID (found in Agent Builder URL when editing agent) : Note - this must be a new Agentforce Service Agent to work (no internal only facing agent for now as those ASA are able to be exposed through the API and the other one won't be able to be reached)
  - Org ID

## Setup Instructions

### 1. Configure Trusted URLs in Salesforce

First, you need to add OpenAI's API domain as a trusted URL and allow microphone access:

1. In Salesforce Setup, navigate to **Security** > **CORS**
2. Add `https://api.openai.com` to the allowed origins. 
4. Go to **Security** > **Session Settings**
5. Under Browser Feature Permissions, ensure you have selected "Trusted URLs Only" for both Camera and Microphone access:

<img width="1632" alt="Capture d’écran 2025-04-23 à 10 27 52" src="https://github.com/user-attachments/assets/bacd95b1-7556-4066-8582-76e560f6119f" />


5. Navigate to **Setup** > **Security** > **Trusted URLs**
6. Click "New" to add a new trusted URL
7. Enter the following:
   - Name: OpenAI
   - URL: https://api.openai.com
8. Save the trusted URL

9. Click on your newly created trusted URL to view details
10. Ensure both "camera" and "microphone" permissions are enabled:

<img width="1655" alt="Capture d’écran 2025-04-23 à 10 28 59" src="https://github.com/user-attachments/assets/49251b41-3894-4d2f-ad1c-3b77e10db5b1" />

Also make sure to add this https://api.openai.com URL and your own current domain url to the whitelisted URLs in Remote site settings ....(see capture below - also note that this is the one url you will have to use later on when setting up your variables after deploying the component)

   ![Capture d’écran 2025-04-29 à 16 37 40](https://github.com/user-attachments/assets/6079a0d6-7b41-4c81-9731-c0c566eb5c74)



### 2. Configure Named Credentials

1. Navigate to **Setup** > **Security** > **Named Credentials**
2. Create a new External Named Credential as shown:

<img width="1663" alt="Capture d’écran 2025-04-23 à 10 31 19" src="https://github.com/user-attachments/assets/57f75bec-d9e2-4056-9703-9a39bde1a84c" />

<img width="1660" alt="Capture d’écran 2025-04-23 à 10 31 26" src="https://github.com/user-attachments/assets/b215db1f-02cd-4118-a0d5-19b35f3d6928" />

Note : if you're unsure of what you need to do on this part, please refer to this link : <a href="https://developer.salesforce.com/docs/einstein/genai/guide/agent-api-get-started.html" target="_blank">Get started with Agentforce API - Create a Connected App</a>. Make sure you're using the right permset (and for now, only those permissions as you may encounter a 400 Error if you're not doing this right).

3. Fill in the following details:
   - Label: OpenAI
   - Name: OpenAI
   - URL: https://api.openai.com
   - Authentication Protocol: No Authentication
   - Save
   - Create a Name credential using the external credential you just created ...
  
<img width="1664" alt="Capture d’écran 2025-04-23 à 10 32 01" src="https://github.com/user-attachments/assets/fb9c2306-1709-4059-80f6-f6fed07c7754" />


4. Navigate to **Setup** > **Security** > **Remote Site Settings**
5. Verify that `https://api.openai.com` is added to your Remote Site Settings

### 3. Deploy the Component

1. Clone or download this repository to your local machine
2. Open a terminal and navigate to the downloaded directory

3. Connect to your Salesforce org by creating an alias:
   ```bash
   sf org login web -a myOrgUrl
   ```

4. Deploy the component to your org:
   ```bash
   sf project deploy start -o myOrgUrl
   ```

### 4. Configure Settings

1. After deployment, navigate to the settings page:
   ```
   https://YOUR_ORG_URL.my.salesforce.com/apex/VoiceAssistantSettings
   ```

2. Fill in your OpenAI and AgentForce settings:
   - **OpenAI Settings**:
     - API Key: Your OpenAI API key
     - Default Model: `gpt-3.5-turbo` (or any other available model)
     - Default Voice: `alloy` (or any other available voice like Arista-PlayAI)
     - Enabled: Checked

   - **AgentForce Settings**:
     - Server URL: Your Salesforce org domain (e.g., `storm-41153b85fca0d8.my.salesforce.com`)
     - Client ID: Your Connected App's Client ID
     - Client Secret: Your Connected App's Client Secret
     - Agent ID: Your AgentForce Agent ID
     - Org ID: Your Salesforce Org ID
     - Enabled: Checked

3. Click "Save" to store your settings
4. Refresh the page after a few seconds
5. Click the "Test OpenAI" and "Test AgentForce" buttons to verify connectivity

### 5. Add the Component to a Lightning Page

1. Navigate to any Lightning page you wish to add the component to
2. Edit the page in Lightning App Builder
3. Find the "Voice Assistant" component in the component list (beware, there is an Agentforce Voice LWC in your list that won't work ... - use the right one)
4. Drag and drop it onto your page
5. Save and activate the page
6. If you're facing design issues, make sure you're using the Lightning Design System 2 or a compatible theme (Cosmos for now in demo Orgs ... if i remember correctly)

## First Use

When using the component for the first time:

1. Your browser will prompt for microphone access - allow it
2. Click the "Hold to Talk" button and speak your query
3. The component will:
   - Transcribe your speech using OpenAI Whisper
   - Send the transcribed text to AgentForce
   - Receive a response from AgentForce
   - Convert the response to speech using OpenAI TTS
   - Play the audio response

## Troubleshooting

- **Microphone not working**: Ensure you've granted microphone permissions in your browser
- **"Refused to load media"**: This is due to Content Security Policy. The component has been updated to handle this by converting base64 audio to Blob URLs
- **No response from AgentForce**: Verify that your AgentForce settings are correct and that your agent is properly configured
- **Audio not playing**: Check that your browser's audio is enabled and not muted
- **Deployment failures**: Ensure you're using the correct Salesforce CLI commands and have necessary permissions

## Technical Details

This component uses:

- **Speech-to-Text**: OpenAI's Whisper model for accurate voice transcription
- **AI Processing**: AgentForce for intelligent query processing
- **Text-to-Speech**: OpenAI's TTS service for natural-sounding voice responses
- **Apex Controllers**: For secure server-side processing
- **Lightning Web Components**: For the browser UI
- **Blob URLs**: To work around Content Security Policy restrictions for audio playback

## Security Notes

- No API keys or credentials are hardcoded in the application
- All sensitive data is stored securely in Salesforce Custom Metadata
- The component uses browser-native APIs for audio capture and playback
- Content Security Policy compliant media handling

## License

[Include license information here]
