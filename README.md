# 📅 AI Appointment Booker — n8n Workflow Documentation

## Overview
The **AI Appointment Booker** workflow automates **AI-driven outbound voice calls** to users who submit a form expressing interest in specific services.  
It integrates **n8n Forms** with the **Retell AI API**, enabling instant, intelligent call scheduling through an AI voice agent.

---

## 🎯 Purpose
To streamline appointment scheduling and service discovery calls by automatically triggering personalized AI voice calls right after a potential client submits their contact details and service interest.

---

## 🔁 Workflow Summary

| Step | Function | Description |
|------|-----------|--------------|
| 1 | **Form Submission Trigger** | Captures user data through a form (name, phone, email, service interest). |
| 2 | **HTTP Request (Retell API)** | Sends data to Retell AI API to initiate an AI-powered booking call. |

---

## 🧩 Node Breakdown

### 1. **On Form Submission**
- **Type:** `n8n-nodes-base.formTrigger`
- **Purpose:** Collects user information via an n8n web form.
- **Form Title:** `Service Discovery Call`
- **Form Description:** “It’s a service call - we’d love to understand your needs and how we can help.”

**Fields Collected:**
| Field Label | Placeholder | Required |
|--------------|-------------|-----------|
| What is your first name? | Damian Dan | ✅ |
| What is the best phone number to reach you? | xxxxxxx | ✅ |
| Please enter your email address. | damiandanxx@gmail.com | ✅ |
| Which service are you most interested in? | Interested in AI Agents and automation services | ✅ |

**Trigger Type:** Webhook (`form submission`)

---

### 2. **HTTP Request**
- **Type:** `n8n-nodes-base.httpRequest`
- **Purpose:** Sends a POST request to **Retell AI API** to create an automated voice call.
- **Method:** `POST`
- **URL:** `https://api.retellai.com/v2/create-phone-call`
- **Headers:**
  ```json
  {
    "Authorization": "Bearer PLEASE_PASTE_YOUR_BEARER_TOKEN_HERE"
  }
  ```

- **Body:**
  ```json
  {
    "from_number": "+441445700144",
    "to_number": "+{{ $json['What is the best phone number to reach you?'] }}",
    "override_agent_id": "agent_fc102823dc204a7b89bb39873c",
    "retell_llm_dynamic_variables": {
      "first_name": "{{ $json['What is your first name?'] }}",
      "email": "{{ $json['Please enter your email address.'] }}",
      "service": "{{ $json['Which service are you most interested in?'] }}"
    }
  }
  ```

- **Notes:**
  - Replace `"PLEASE_PASTE_YOUR_BEARER_TOKEN_HERE"` with your valid **Retell API token**.
  - `override_agent_id` specifies which AI voice agent will handle the call.
  - Dynamic variables personalize the AI conversation for each lead.

---

## ⚙️ Workflow Connections
```text
[On form submission] ──▶ [HTTP Request → Retell AI API]
```

---

## 🧾 Example Data Flow

| Field | Value | Description |
|--------|--------|-------------|
| first_name | Damian Dan | Passed to Retell as a dynamic variable |
| phone_number | +441234567890 | Used as the recipient number |
| email | damiandanxx@gmail.com | Shared with Retell for context |
| service | AI Agents and Automation | Guides the AI conversation topic |

---

## 🧩 Key Configuration Parameters
| Parameter | Description |
|------------|-------------|
| `from_number` | The number Retell AI uses to make outbound calls |
| `to_number` | The client’s phone number collected from the form |
| `override_agent_id` | Retell AI voice agent ID to use for the call |
| `retell_llm_dynamic_variables` | Context variables for personalized dialogue |

---

## 🔒 Security & Credentials
- **Retell AI Authorization Token:** Required in the header (`Bearer <token>`).
- Ensure your API key is stored securely using n8n **Credentials Manager**.
- Avoid hardcoding sensitive values directly in the node body.

---

## ⚠️ Common Setup Notes
1. Update your **Bearer token** before execution.
2. Ensure Retell AI Agent ID is correct and active.
3. Validate phone number format (`+countrycodeXXXXXXXXXX`).
4. Enable workflow only after testing via a sandbox environment.

---

## 🧠 Example Scenario
A potential client fills out the discovery call form on your website.  
Immediately, this workflow triggers — **AI Appointment Booker** places a **customized phone call** using the client’s name, service interest, and other form data to schedule a discovery session or consultation.

---

## 🧩 Version Info
- **Workflow Name:** AI Appointment Booker  
- **Workflow Version:** v1.0  
- **Execution Order:** Sequential (v1)  
- **Active:** No (manual activation required)  
- **Platform:** n8n Automation  

---

## ✅ Integration Summary
| Platform | Purpose |
|-----------|----------|
| **n8n Forms** | Collect user data |
| **Retell AI API** | Initiate AI-powered appointment calls |

---

## 📄 Example API Response
```json
{
  "call_id": "call_18b76f9a9fdd4b90ac30a992",
  "status": "initiated",
  "to_number": "+441234567890",
  "agent": "agent_fc102823dc204a7b89bb39873c"
}
```

---

## 🧩 Workflow Diagram (Simplified)
```mermaid
graph LR
    A[User Submits Form] --> B[n8n Form Trigger]
    B --> C[HTTP Request Node]
    C --> D[Retell AI API → Initiate Appointment Call]
    D --> E[AI Voice Agent Schedules Appointment]
```
