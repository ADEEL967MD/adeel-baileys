# ADEEL-BAILEYS (modded Baileys, by Adeel)

Base: official `@whiskeysockets/baileys` **7.0.0-rc14** (unmodified except the 3 patches below).
Package name: `adeel-md-baileys`, version `1.0.0`. Bot code needs ZERO changes — it is used
through the same npm-alias trick as Elaina (see "Use" below).

## What is patched in (vs clean rc14)

1. **Group status send (gstatus fix)** — `lib/Utils/messages.js` (+14 lines) + `lib/Socket/messages-send.js` (+8 lines):
   - Content with `groupStatus: true` is wrapped in `groupStatusMessageV2` with inner
     `contextInfo.isGroupStatus` set (same mechanism as modded Baileys).
   - `relayMessage` adds `<meta is_group_status="true"/>` to the stanza for such messages.
   - Result: group status posts as status ONLY, no double post in the group chat.
   - Verified: text + groupStatus produces the V2 wrap; without groupStatus nothing changes.
2. **reportSpam (report command)** — `lib/Socket/chats.js` (+52 lines):
   - `sock.reportSpam(jid, options)` added (spam IQ, same as modded Baileys), with
     `buildSpamListNode` / `readSpamReportResult` helpers. Default flow `overflow_menu_report`;
     any flow string can be passed via `options.flow`, plus `subject` / `source` / `isKnownChat`.
   - Verified: spam node shape, invalid-JID 400, server-error propagation.
3. **Channel voice needs NO patch**: rc14 already has `newsletterMetadata` + `sendMessage` to
   `@newsletter` JIDs, so the chvoice command works on this build as-is.

## What is NOT in this build

- **Channel poll vote** (`newsletterSendPollVote` / poll-vote encryption): a separate, larger backport.
  Not included here.
- **Channel status** (`sendNewsletterStatus`): cannot be added by ANY fork — WhatsApp grants the
  `CHANNEL_STATUS_PRODUCER` capability server-side, per channel. No code can force it.

## Use (upload it yourself, then point the bot at it)

Option A — GitHub: push this folder to a repo, then in the BOT's package.json replace the
Baileys line with (example repo name `adeel-baileys`):

    "@whiskeysockets/baileys": "github:ADEEL967MD/adeel-baileys"

Option B — npm: publish this package (rename first if the name is taken), then in the BOT's
package.json:

    "@whiskeysockets/baileys": "npm:adeel-md-baileys@1.0.0"

Then redeploy/restart the bot. No require changes anywhere — the bot still imports
`@whiskeysockets/baileys`.

## Live test still needed

The group-status end-to-end test: replace the bot's gstatus plugin with the fixed one,
restart the bot, post a group status, and confirm it appears ONLY as status (no chat post).
