# D.E.M. Mission — Operator's Manual

Decoder widget for the **FH Conference 2026 · VSGT Maribor**.
Built by Mr. Mitja Petelin.

---

## What this is

A spy-themed cipher puzzle played by ~35 conference delegates across 2 days. They find encoded clue cards hidden at the venue, decode them with a Caesar cipher wheel (physical or online), and submit decoded solutions to a live intelligence feed. First delegate to crack the full phrase **DEUS EX MACHINA** wins.

---

## Live URLs

- **Conference site:** `https://YOUR-NETLIFY-URL.netlify.app` (set after deployment)
- **Supabase dashboard:** https://supabase.com/dashboard
- **GitHub repo:** https://github.com/mitjapetelin-sudo/D.E.M.-Mission-decoder

---

## File structure

```
D.E.M.-Mission-decoder/
├── index.html       ← the widget. don't touch during the conference.
├── clues.json       ← the 30 clues. edit this to add/remove/modify clues live.
└── README.md        ← this file.
```

---

## How to edit clues during the conference

The whole point of this setup is that you can drop new clues mid-event by editing `clues.json` on GitHub from your phone or laptop. Netlify auto-redeploys in ~30 seconds.

### Add a new clue

1. Go to https://github.com/mitjapetelin-sudo/D.E.M.-Mission-decoder
2. Click `clues.json`
3. Click the pencil icon (top right) to edit
4. Add a new entry inside the `"clues"` array. Use the next available `id`:
   ```json
   { "id": 31, "cipher": "QHZ FLSKHU KHUH", "sol": "NEW CIPHER HERE", "tier": "clear" }
   ```
5. Make sure to add a comma after the previous entry
6. Scroll down, click **Commit changes**
7. Wait 30 seconds, refresh the live site — new clue is now accepted

### To encode a new clue (Caesar +3)

Use the widget itself: open the live site, type the plain solution into the "Solution" field, and the "Type in the letters" field shows the cipher. Or use any online Caesar cipher tool with shift +3.

Examples:
| Plain | Cipher (+3) |
|---|---|
| `HELLO` | `KHOOR` |
| `THE END IS NEAR` | `WKH HQG LV QHDU` |

### Disable a clue

Either delete it entirely, or wrap the line in a way that breaks it (easiest: change `"id": 7` to `"id": "7_disabled"`). Simpler: just delete the line.

### Watch out for

- **Trailing commas** break JSON. The last entry should not have a comma after it.
- **Smart quotes** break JSON. Always use straight `"` quotes.
- **Duplicate IDs** cause weird behavior. Each clue needs a unique `id`.

---

## How to view live submissions

Log into https://supabase.com/dashboard → your project → **Table Editor** → `submissions` table.

You'll see every submission in real time. Columns:
- `clue_id` — which clue they solved
- `solution` — the text they submitted
- `agent_name` — who submitted it
- `created_at` — timestamp

The `mission_state` table contains one row showing who (if anyone) has solved the full mission.

---

## How to manually publish a clue

If delegates are stuck and you want to release a clue without anyone solving it:

1. Supabase dashboard → Table Editor → `submissions` → **Insert row**
2. Fill in:
   - `clue_id`: the clue ID
   - `solution`: the exact solution text from `clues.json`
   - `agent_name`: `Agent Q`
3. Click Save.

Every connected widget shows it immediately in the feed.

---

## How to reset the mission (e.g., for rehearsal)

1. Supabase dashboard → Table Editor → `submissions` → select all rows → delete
2. Table Editor → `mission_state` → edit the single row → clear `solved_by` and `solved_at` fields → save

All widgets revert to "no transmissions recovered."

---

## Cooldown for individual delegates

If a delegate gets locked out (3 wrong attempts → 30 min cooldown), and you want to unlock them, have them open the browser console (F12) and run:

```javascript
localStorage.removeItem('dem_cooldown'); localStorage.removeItem('dem_rejections');
```

Or just refresh and have them switch browsers/devices.

---

## Embedding on the conference website

The `index.html` is a complete, standalone page. To embed it inside another site (instead of as its own page), wrap it in an iframe:

```html
<iframe
  src="https://YOUR-NETLIFY-URL.netlify.app"
  style="width:100%; height:1400px; border:none; background:#000;"
  title="D.E.M. Mission Decoder">
</iframe>
```

Adjust `height` so it fits without scrollbars.

---

## Troubleshooting

**Widget loads but says "Loading transmissions…" forever**
→ Supabase connection issue. Check Supabase project is not paused (free tier pauses after 7 days of inactivity — just open the dashboard once a week before the event).

**Submissions vanish after submission**
→ Row-level security policy issue. Re-run the SQL setup script in Supabase SQL Editor.

**Same clue keeps getting accepted multiple times**
→ Check that the clue's `id` is unique in `clues.json`. Duplicate IDs confuse the dedupe logic.

**Real-time feed doesn't update without refresh**
→ Open browser console (F12) and look for WebSocket errors. Usually a network issue, not the widget.

---

## Credits

Designed, built, and launched by **Mr. Mitja Petelin**
© 2026 Mitja Petelin · All rights reserved
Created for the FH Conference 2026 · VSGT Maribor
