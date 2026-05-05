# Daily Grant Deadline Digest

The cron channel fires once on its schedule. There is no payload
to parse — your job is to run the deadline-alert workflow and post
the result to Slack.

## Steps

1. Follow the **Deadline Alert Workflow** in SOUL.md (Phases 1–4):
   resolve the grant tracker spreadsheet (`GRANT_SHEET_ID` first,
   else title match), read the rows, bucket upcoming deadlines into
   30 / 60 / 90 days, flag stale drafts, and compose the digest
   message.
2. Resolve target channels per the SOUL **Where to post** section:
   list every channel the bot is a member of and post once to each.
   If the bot is in zero channels, DM the workspace install user
   instead with the digest and a one-line invite hint.
3. Post exactly once per resolved destination. Do not retry on
   failure — log the error in your session and continue with the
   remaining destinations. The next cron fire is the recovery.
4. Do not send any follow-ups, reactions, or thread replies after
   the initial post. Your turn ends after the posts complete.

## Skip conditions

Skip posting (and stop silently) if any of these are true:

- The grant tracker spreadsheet can't be found (no `GRANT_SHEET_ID`
  set and no title match for `Grants` / `Grant Tracker` /
  `Funding`). DM the workspace install user once with the issue —
  do not post to channels.
- Every bucket is empty: no grants due in the next 90 days, and no
  Drafting rows that have stalled inside a 60-day window. Stay
  silent rather than posting an empty digest.
