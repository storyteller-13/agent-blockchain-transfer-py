# Blockchain transfer alert agent

Email alerts via [Resend](https://resend.com) when transfers hit watched addresses (Bitcoin, Ethereum, Cardano, TON, Tron, NEAR, Zcash, XRP).

## Local setup

1. **Dependencies**: `make install` (runs `npm install`).

2. **Environment** — copy `.env.example` to `.env`:

   | Variable | Notes |
   |----------|--------|
   | `RESEND_API_KEY` | Required. From [Resend API keys](https://resend.com/api-keys). |
   | `NOTIFY_EMAIL` | Required. Recipient for alerts. |
   | `FROM_EMAIL` | Optional. Production: [verified domain](https://resend.com/domains). Test: `Alerts <onboarding@resend.dev>`. |
   | `CONFIG_PATH` | Optional. Default `./config.json`. |
   | `ETHEREUM_RPC_URL` | Optional. Prefer Alchemy/Infura in production. |
   | `POLL_INTERVAL_SEC` | Optional. Default `30`. |
   | `DISABLE_ZCASH` | Optional. `1` if Blockchair limits bite. |
   | `BLOCKCHAIR_API_KEY` | Optional. For Zcash via Blockchair. |

3. **Addresses** — copy `config.example.json` to `config.json` and fill per-chain arrays.

4. **Run**: `make server` (same as `npm start` → `node src/index.js`).

## Code map

- `src/index.js` — local polling agent entry.
- `src/monitors/*.js` — per-chain monitors.
- `api/cron.js` — Vercel cron handler.
- `vercel.json` — cron schedule.

## Vercel deployment

1. **CLI** (optional): `npm i -g vercel`, `vercel login`, then `vercel` or `vercel --prod` from repo root.

2. **Dashboard**: Import repo at [vercel.com/new](https://vercel.com/new). Defaults for build/output are fine.

3. **Environment variables**:

   | Name | Required | Notes |
   |------|----------|--------|
   | `RESEND_API_KEY` | Yes | |
   | `NOTIFY_EMAIL` | Yes | |
   | `CONFIG_JSON` | Yes | Single-line JSON (same shape as `config.json`). |
   | `FROM_EMAIL` | No | |
   | `ETHEREUM_RPC_URL` | No | |
   | `DISABLE_ZCASH` | No | `1` to skip Zcash. |
   | `CRON_SECRET` | No | If set, cron requests need `Authorization: Bearer …`. |

4. **`CONFIG_JSON`** — one line, no line breaks. Example (replace addresses):

   ```json
   {"bitcoin":["bc1q6yrkch06uwmkkesy5fvfvj00kzm3cqld0w7znu"],"ethereum":["0xe54E5a3ACaa91a2EbFa26cD21372BF9f7E1F1c22"],"cardano":["addr1qxdysr0kh68u63zt5mpk7d5kt8an5n3rxs3t0k2t02sz8e4rhf003andeq6vn9ga4p5dqtkczgnmz0xt3zt2ju7edlws7yfpz0"],"ton":["UQA-EpweW4yj_SJD-8SMAx74oUDMqDOW4YVc_ut4TzXog5nl"],"tron":["TFT1PY2pMaf1czd6MsUuziossBD73sZBRJ"],"near":["fc5de7f6f607f872232e8491a755a5a9656d076ce35d2d5dab800c7250900cb4"],"zcash":["t1em7Vhge9HKi9BYoBi1tADg2eMe5zj3Fvf"],"xrp":["rNyCCdva7q12PBGqe6xYD9fLCZ5yEMjebP"]}
   ```

5. **Vercel KV** (cron checkpoint): Project → Storage → Create → KV. Connect to the project so `KV_REST_API_URL` and `KV_REST_API_TOKEN` are set.

6. **Cron**: `vercel.json` triggers `/api/cron` (interval depends on Vercel plan).

7. **Smoke test**: `https://<project>.vercel.app/api/cron` → expect `{"ok":true}`. With `CRON_SECRET`:

   ```bash
   curl -H "Authorization: Bearer YOUR_CRON_SECRET" "https://<project>.vercel.app/api/cron"
   ```
