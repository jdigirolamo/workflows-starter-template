# TradeForge Workflow Engine

A real-time, durable automation engine built on **Cloudflare Workflows**, **Durable Objects**, and **WebSockets**. Part of the TradeForge platform — an AI-powered CRM and automation suite for Alberta's trades industry.

## What It Does

- Runs multi-step, durable workflows that survive restarts and failures
- Delivers real-time status updates to clients via WebSockets
- Handles time-based delays and event-driven workflow pauses
- Powers automation pipelines including missed-call follow-up, lead nurturing, and job scheduling for trade businesses

## Tech Stack

| Layer | Technology |
| --- | --- |
| Runtime | Cloudflare Workers |
| Workflow Engine | Cloudflare Workflows |
| Real-time State | Durable Objects |
| WebSocket Layer | Cloudflare Workers WebSocket API |
| Frontend | Vite + TypeScript |
| Styling | Tailwind CSS |
| Testing | Vitest |

## Project Structure

```
workflows-starter-template/
├── src/                    # Frontend React/TypeScript app
├── worker/                 # Cloudflare Worker + Workflows backend
│   ├── workflows/          # Workflow definitions (multi-step jobs)
│   └── index.ts            # Worker entry point with WebSocket support
├── test/                   # Vitest test suite
├── assets/                 # Static assets
├── index.html              # App entry point
├── wrangler.json           # Cloudflare Worker + Durable Object config
├── tailwind.config.js      # Tailwind CSS config
├── tsconfig.json           # TypeScript config
└── package.json
```

## Setup

### Prerequisites

- Node.js 18+
- Cloudflare account with Workers and Workflows enabled
- Wrangler CLI: `npm install -g wrangler`

### Local Development

```bash
npm install
npm run dev
```

Visit `http://localhost:5173` to see the interactive workflow dashboard.

### Deploy

```bash
npm run deploy
```

### Run Tests

```bash
npm test
```

## How Workflows Work

Each workflow is a class that extends Cloudflare's `WorkflowEntrypoint`:

```typescript
export class LeadFollowUpWorkflow extends WorkflowEntrypoint {
  async run(event: WorkflowEvent, step: WorkflowStep) {
    // Step 1: Send immediate SMS
    await step.do('send-initial-sms', async () => {
      await sendSMS(event.payload.phone, 'Thanks for reaching out!');
    });

    // Step 2: Wait 24 hours
    await step.sleep('wait-24h', '24 hours');

    // Step 3: Send follow-up
    await step.do('send-followup', async () => {
      await sendSMS(event.payload.phone, 'Still interested in a quote?');
    });
  }
}
```

Workflows are durable — if the Worker restarts mid-execution, the workflow resumes from the last completed step.

## Part of the TradeForge Platform

This engine powers background automation for the **TradeForge** CRM system — managing lead follow-up sequences, appointment reminders, and job status notifications for HVAC, plumbing, electrical, and general contracting businesses in Calgary and Alberta.

Related systems:

- **TradeForge AI Proxy** — Cloudflare Worker + D1, AI model API routing
- **TradeForge CRM** — lead tracking, SMS follow-up, job pipeline
- **AI Receptionist** — missed call → SMS automation via n8n + Claude API

## License

MIT
