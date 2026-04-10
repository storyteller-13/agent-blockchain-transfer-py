# Bill — blockchain transfer alert agent

Sends email (via [Resend](https://resend.com)) when a transfer is detected to a watched address on Bitcoin, Ethereum, Cardano, TON, Tron, NEAR, Zcash, or XRP.

## Quick start

```bash
make install
cp .env.example .env   # add RESEND_API_KEY, NOTIFY_EMAIL, etc.
cp config.example.json config.json
make server
```

**Full setup, environment reference, Vercel deploy, and cron/KV details:** [`docs/SKILL.md`](docs/SKILL.md).

Deploy-only entry point: [`docs/DEPLOY.md`](docs/DEPLOY.md).
