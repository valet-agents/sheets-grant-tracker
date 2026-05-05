This folder contains the source for a Skilled Agent originally built for the Valet runtime. Changes should follow the Skilled Agent open standard.

## Setup

### Connectors

- **google-sheets**: Google Sheets via OAuth. The agent uses it to read the grant tracker spreadsheet, parse rows (funder, amount, due date, status, owner, notes), and — only when a Slack user explicitly confirms — update cells or append rows. Add it from the catalog at the org level so other Sheets-powered agents can share the same OAuth grant.

### Channels

- **slack** (slack): The agent's per-agent Slack bot. Listens for @mentions and replies in-thread, and posts the daily grant digest to whichever channels the bot has been invited to. Slack writes use the auto-injected outbound Slack connector.
- **cron** (cron): Fires the daily grant digest at 9am Pacific, Monday through Friday (`0 9 * * 1-5`, `America/Los_Angeles`). Declared inline in `valet.yaml`, so it's created automatically by the dashboard setup flow.

### Secrets

This agent uses the OAuth variant of Google Sheets, so no API key is needed at the org or agent level. The OAuth grant happens in the dashboard setup flow when you connect Google Sheets.

### External Setup

1. After deploy, complete the Google Sheets OAuth in the dashboard — grant access to the spreadsheet that holds your grant tracker.
2. Set the `GRANT_SHEET_ID` env var on the agent to the ID of the grant tracker spreadsheet (the long string in the Google Sheets URL between `/d/` and `/edit`). If you skip this, the agent falls back to the first spreadsheet it can reach whose title matches `Grants`, `Grant Tracker`, or `Funding`.
3. Invite the agent's Slack bot to whichever channel(s) you want the daily digest in. The agent posts to every channel it's a member of — invite it to one focused channel, or several. If the bot has not been invited anywhere, the digest is sent as a DM to the workspace install user with a one-line nudge to invite it somewhere.
4. Invite the bot to any additional channels where teammates should be able to @mention it for ad-hoc grant questions (e.g. a development or fundraising channel for "what's due in the next 30 days?" follow-ups).
5. The first cron fire is the next 9am Pacific weekday after deploy. To smoke-test sooner, @mention the bot in Slack with a question like *"what grants are due in the next 30 days?"* — that exercises the Slack + Sheets path without waiting for the cron.

## Customizing

- **Change the schedule**: edit the `cron` and `timezone` on the `cron` channel in `valet.yaml`, then redeploy.
- **Control where the digest posts**: invite or remove the bot from channels in Slack — that's the only signal the agent uses. There is no channel name in the configuration.
- **Point at a different sheet**: change `GRANT_SHEET_ID` on the agent. The SOUL workflow honors the env var first and falls back to title matching only when it's unset.
- **Adjust the buckets**: edit the 30 / 60 / 90 day thresholds and the 14-day stale-draft window in `SOUL.md` (the **Bucket the deadlines** phase) and redeploy.
