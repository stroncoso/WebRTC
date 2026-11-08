# WebRTC use cases: SIM-less devices and AI voice agents

Two use cases showing how the WebRTC APIs let devices and services without a SIM card place and receive regular phone calls through the operator's IMS network. For the detailed Application Service Provider scenario, see the [WebRTC API user story](WebRTC-API-User-Story_ASP-WebRTCGW.md).

## Calling from SIM-less devices

| | |
|---|---|
| **Sectors** | Consumer electronics, Smart home, Automotive, Retail, Public services |
| **CAMARA APIs** | WebRTC Registration, WebRTC Call Handling, WebRTC Events Subscriptions |
| **GSMA Use Case Library** | [Enhanced call experiences](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma_resources/enhanced-call-experiences/) |

**Business problem being solved**

Smart TVs, smart speakers, laptops, kiosks and cars are connected to the internet but have no SIM card, so they cannot use the subscriber's phone number to call or be called. Building and operating a telephony integration for each device or platform is costly and creates vendor lock-in.

**How the solution works**

The device app uses the WebRTC APIs to act as another line of the subscriber's phone number. It places and receives regular phone calls over the operator's network, with no SIP stack on the device:

- **WebRTC Registration:** registers the device with the subscriber's phone number and keeps the registration alive with periodic refreshes.
- **WebRTC Events Subscriptions:** notifies the app of incoming calls and call status changes through a webhook.
- **WebRTC Call Handling:** places calls to any phone number, answers incoming calls, and ends them; emergency calls can include the caller's location.

**Example**

A family uses the Smart TV app to video-call a grandparent's mobile phone. When the grandparent calls back that number, the call rings on the TV.

## AI voice agents on the phone network

| | |
|---|---|
| **Sectors** | Customer service, Healthcare, Travel and Hospitality, Financial services, All sectors serving large customer bases |
| **CAMARA APIs** | WebRTC Registration, WebRTC Call Handling, WebRTC Events Subscriptions |
| **GSMA Use Case Library** | [Enhanced call experiences](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma_resources/enhanced-call-experiences/) |

**Business problem being solved**

Businesses deploy AI voice agents to answer customer calls, book appointments or provide live transcription and translation. These agents run in the cloud and stream audio over WebRTC, but connecting them to real phone numbers usually requires third-party SIP trunks and dedicated telephony infrastructure.

**How the solution works**

The AI service uses the WebRTC APIs to answer and place calls on a business phone number directly through the operator's network:

- **WebRTC Registration:** registers the AI service on the business phone number; several numbers can be registered (e.g. one per department or language).
- **WebRTC Events Subscriptions:** delivers each incoming call to the AI service as an event, so it scales with call volume without polling.
- **WebRTC Call Handling:** answers the call and connects the caller's audio to the AI agent, places outbound calls (e.g. reminders), and ends the call when finished.

**Example**

A clinic's AI receptionist answers calls to the clinic's phone number, books appointments, and hands complex requests over to staff.

## Typical flow

Both use cases follow the same steps:

1. **Subscribe:** the application subscribes to `session-invitation`, `session-status` and `registration-ends` events, providing its webhook URL.
2. **Register:** the application registers the device or service with the phone number and refreshes the registration before `expiresAt`.
3. **Call:**
   - Outbound: the application creates a session with the destination number and its SDP offer, and follows progress through `session-status` events.
   - Inbound: the application receives a `session-invitation` event and answers by updating the session status with its SDP answer.
4. **End:** either party terminates the session; when the device or service goes offline, the application removes its registration.

See [Obtaining a WebRTC SDP to create calls](webrtc-call-handling-sdp-gathering.md) for how to produce the SDP offer and answer.
