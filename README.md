# Pay for the OpenAI API with crypto (USDT): GPT-6, Codex and the OpenAI SDK without a card

> Maintained by the TeamoRouter team. The gateway used in this guide is ours.

OpenAI declined your card, or the API answers `403 unsupported_country_region_territory`? Call GPT models through [TeamoRouter](https://teamorouter.com/?utm_source=github&utm_medium=readme&utm_campaign=b6_pay-openai-api-with-crypto) instead: the official OpenAI SDK talks to `api.teamorouter.com`, and you pay with USDT.

## Short answer

- **What changes in your code:** the base URL (`https://api.teamorouter.com/v1`) and the key (`sk-teamo-...`). Requests, responses and streaming stay in the standard OpenAI format.
- **How you pay:** click **Pay with crypto** in the top-up dialog, choose a coin (USDT, USDC or ETH) and then a network on the checkout page, and send it to the address the page shows from your exchange app (Binance, OKX and others) or any wallet. The checkout shows which networks your amount supports (BEP20 works from $5). No browser extension, and it works on a phone. Minimum $5.
- **What you get:** one key for GPT (`gpt-6-sol`, `gpt-6-luna`, `gpt-6-astra` and others), Claude, Gemini, DeepSeek and more, billed per token with no subscription.
- **What you don't need:** an OpenAI account, a ChatGPT login or a credit card.

## Point the OpenAI SDK at TeamoRouter

**Python** (`pip install openai`):

```python
from openai import OpenAI

client = OpenAI(
    api_key="sk-teamo-xxxxxx",
    base_url="https://api.teamorouter.com/v1",
)

resp = client.chat.completions.create(
    model="gpt-5.6-sol",
    messages=[{"role": "user", "content": "Hello"}],
)
print(resp.choices[0].message.content)
```

**Node.js** (`npm install openai`), Responses API:

```javascript
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: "sk-teamo-xxxxxx",
  baseURL: "https://api.teamorouter.com/v1",
});

const resp = await client.responses.create({
  model: "gpt-5.6-sol",
  input: "Introduce TeamoRouter in one sentence",
});
console.log(resp.output_text);
```

**No code change**, for tools that read the standard environment variables:

```bash
export OPENAI_BASE_URL="https://api.teamorouter.com/v1"
export OPENAI_API_KEY="sk-teamo-xxxxxx"
```

Good to know:

- Both `/v1/chat/completions` and `/v1/responses` work; `/v1/responses` is for GPT models only.
- List the models your key can call: `curl https://api.teamorouter.com/v1/models -H "Authorization: Bearer sk-teamo-xxxxxx"`.
- Optional Fast mode: `service_tier: "fast"` (`gpt-5.6-sol` up to 2.5x faster, billed at 2x).

Full reference: [API integration guide](https://teamorouter.com/docs/api-integration?utm_source=github&utm_medium=readme&utm_campaign=b6_pay-openai-api-with-crypto).

## Codex CLI

Install Codex (Node.js 22+) with `npm install -g @openai/codex`, then add the provider and key. macOS / Linux:

```bash
mkdir -p ~/.codex && cat > ~/.codex/config.toml <<'EOF'
model_provider = "teamorouter"
model = "gpt-6-sol"
model_reasoning_effort = "high"
[model_providers.teamorouter]
name = "TeamoRouter"
base_url = "https://api.teamorouter.com/v1"
env_key = "OPENAI_API_KEY"
wire_api = "responses"
EOF
echo 'export OPENAI_API_KEY="<your-TeamoRouter-key>"' >> ~/.zshrc   # ~/.bashrc on Linux
source ~/.zshrc
```

Run `codex`. Windows (PowerShell) steps are in the [Codex CLI guide](https://teamorouter.com/docs/install-codex?utm_source=github&utm_medium=readme&utm_campaign=b6_pay-openai-api-with-crypto). Inside a session, `/fast` toggles Fast mode.

## Top up with USDT, step by step

You need a TeamoRouter account and USDT, USDC or ETH on an exchange or in a wallet, enough to cover the top-up amount plus the withdrawal fee. That's all: the checkout page is an ordinary web page, so a phone with your exchange app is enough.

### Supported coins and networks

The checkout accepts USDT, USDC and ETH. Which coins and networks it offers depends on the order amount. As of 2026-10-20, the checkout shows what's available for your amount:

| Order amount | What the checkout offers |
|---|---|
| From $5 | ETH, USDC on BNB Smart Chain (BEP20), USDT on BNB Smart Chain (BEP20) |
| From $10 | Also USDC on Base and USDT on Ethereum (ERC20) |
| From $30 | Also USDT on Tron (TRC20) |

For small amounts, pick **USDT on BEP20**: it's available from $5, and Binance charges about 0.01 USDT to withdraw on BEP20, against about 1.5 USDT on TRC20. OKX doesn't offer USDT withdrawals on BEP20; from OKX, top up $10 or more and use ERC20, which costs less there than TRC20.

Send only the coin and network you picked; anything else sent to the checkout address doesn't count as payment. Withdrawal fees are set by each exchange and change over time, so check the fee on the withdrawal screen.

### Steps

1. Sign in to TeamoRouter, open the top-up dialog in the console and choose an amount ($5 or more). The amount decides which coins and networks the checkout offers.
2. Click **Pay with crypto** (on a narrow screen the button reads **Crypto**). You're taken to the checkout page.
3. The checkout page opens by asking you to choose a coin (USDT, USDC or ETH), then the network you'll send on. Only the networks available for your amount are listed. The page shows how much to send and a deposit address. The invoice is valid for **60 minutes**.
4. In your exchange app, start a withdrawal (in a wallet, a send): choose **the same coin on the same network**, paste the address from the checkout page, and enter the amount so that what arrives is **no less than the amount on the page**.
5. Wait for the network to confirm the transfer. Your balance is credited once the payment is confirmed, even if you close the checkout page. If it hasn't shown up yet, refresh the console in a while and don't pay again.
6. When the balance arrives, [create an API key](https://teamorouter.com/dashboard?tab=api-keys&utm_source=github&utm_medium=readme&utm_campaign=b6_pay-openai-api-with-crypto).

### What makes a crypto payment fail

- **A different coin or network.** Withdraw the coin you picked, on the network you picked on the checkout page. BEP20 and ERC20 addresses both start with `0x`, so an exchange will let you send USDT on ERC20 to a BEP20 address, but that transfer doesn't count as payment for the invoice. Exchanges name networks differently: BEP20 may appear as BSC or BNB Smart Chain, TRC20 as Tron, ERC20 as Ethereum. If your exchange doesn't offer that coin on that network, go back to the checkout page and pick one it does.
- **The withdrawal fee eats into the amount.** Many exchanges take the fee out of the amount you type. Before confirming, check the "amount received" line on the withdrawal screen: it must be at least the amount on the checkout page; if the fee comes out of your amount, add it on top. An invoice up to 1% short still counts as paid. If more is missing, don't send a second transfer; contact support with your TxID. If you send more than the invoice, your balance is credited with the order amount, and you can contact support about the extra.
- **The invoice expires.** Exchanges sometimes hold a withdrawal for manual review, which can take longer than 60 minutes. Make sure the USDT is already in the exchange account before you click **Pay with crypto**. Expired before you sent anything: renew the invoice on the checkout page, or start a new top-up, and send to the address that's currently valid, not one you copied earlier. Expired after you already sent: don't send again; renew the invoice on the checkout page, and if that doesn't resolve it, contact support with your TxID.

### Where to find your TxID

The TxID (also called the transaction hash or Txn Hash) identifies your transfer on chain, and support needs it to trace a payment.

- **Exchange app:** open your withdrawal history, tap this withdrawal, and copy the TxID from its details. It only appears once the exchange has actually sent the transfer; a withdrawal still under review doesn't have one yet.
- **Wallet:** open the transaction in the wallet's history; the hash is in the details.

When you contact support, include the TxID, the network, the amount, the time you sent it and the TeamoRouter account you topped up.

Full walkthrough: [Pay for Claude and GPT APIs with Crypto (USDT)](https://teamorouter.com/blogs/pay-claude-with-usdt?utm_source=github&utm_medium=readme&utm_campaign=b6_pay-openai-api-with-crypto).

## Already have USDT on X Layer? Pay with OKX Wallet

OKX Wallet works like any other wallet on the checkout page: pick a coin and network there and send from the wallet. Sending from a wallet needs that network's coin for gas, for example BNB on BEP20.

If your USDT is already on X Layer (shown on-chain as USD₮0), you can skip the checkout page. With the [OKX Wallet extension](https://chromewebstore.google.com/detail/okx-wallet/mcohilncbfahbmgdjkbpemcciiolgcge) installed and unlocked in a desktop browser (Chrome, Edge or Brave), the top-up dialog shows an extra link, **Have USDT on X Layer? Pay with OKX Wallet**. Use that link, not the **Pay with crypto** button:

1. Choose an amount ($5 minimum) and click **Have USDT on X Layer? Pay with OKX Wallet**.
2. Approve the wallet connection, then approve switching to X Layer (the first time, adding it).
3. Sign the request: an EIP-3009 `TransferWithAuthorization` for this amount only, usable once within its validity window, with no access to anything else in your wallet. The transfer is submitted for you, so you need no OKB for gas. Some wallets show 6-decimal units: `5000000` = 5 USDT.

The USD₮0 must be in the wallet on X Layer; exchange balances and USDT on other chains don't count. To move USDT to X Layer:

| Your USDT is on | Route |
|---|---|
| OKX | Assets → Withdraw → USDT → network **X Layer** → your OKX Wallet address |
| Binance | Withdraw on **Arbitrum** to your OKX Wallet address (that USDT is USDT0), then move it to X Layer on [usdt0.to](https://usdt0.to). Needs a little ETH on Arbitrum for gas |
| Another exchange | Send to your OKX account over a network both support (e.g. TRC20), then withdraw to X Layer |
| A wallet on Tron, Ethereum, Arbitrum… | Transfer page on [usdt0.to](https://usdt0.to): source = your chain, destination = X Layer, recipient = your OKX Wallet address. The page shows whether the route works; gas is paid on the source chain |

Errors on this route:

| You see | Fix |
|---|---|
| No wallet found | Install and unlock OKX Wallet, reload. Use a desktop browser; mobile ones usually have no wallet extension |
| `insufficient_balance` when paying | Not enough USD₮0 **on X Layer**. USDT on other chains or on an exchange doesn't count |
| Code `-32603` | Unlock OKX Wallet, disable other wallet extensions such as MetaMask, reload |
| Network switch failed | Switch to X Layer manually in the wallet and retry |

## Common errors

| You see | Fix |
|---|---|
| Paid, but the balance hasn't changed | Don't pay again; the network may still be confirming. Refresh the console in a while. If the invoice expired or the amount was short, see "What makes a crypto payment fail" above, and contact support with your TxID if it's still unresolved |
| API `401` | Key copied incompletely or deleted in the console |
| API `402 insufficient_balance` | Balance can't cover the request; long-context requests reserve their maximum cost first |
| Codex `404 … url: https://api.teamorouter.com/responses` | `/v1` is missing from `base_url` |

## FAQ

**Do I need an OpenAI account or ChatGPT Plus?** No. You call `api.teamorouter.com` with a TeamoRouter key and pay only for tokens.

**Do Codex usage limits apply?** The limits that reset every few hours or weekly belong to ChatGPT plans. With an API key you pay per token as long as you have balance.

**Can the same key call Claude?** Yes, on the Anthropic native protocol: the Anthropic SDK with `base_url="https://api.teamorouter.com"` (no `/v1`), or [Claude Code](https://teamorouter.com/docs/install-claude-code?utm_source=github&utm_medium=readme&utm_campaign=b6_pay-openai-api-with-crypto) via `ANTHROPIC_BASE_URL`. Through the OpenAI format, Claude can lose prompt caching and thinking.

**Are there payment fees?** The checkout page doesn't add a processing fee. You pay your exchange's withdrawal fee, or network gas if you send from a wallet.

**Can I pay on my phone?** Yes. Open the checkout page on your phone and copy the address and amount into your exchange app.

**What does it cost?** Per-token prices for each model are on the [pricing page](https://teamorouter.com/?utm_source=github&utm_medium=readme&utm_campaign=b6_pay-openai-api-with-crypto#pricing). No subscription.

## Links

- [TeamoRouter home](https://teamorouter.com/?utm_source=github&utm_medium=readme&utm_campaign=b6_pay-openai-api-with-crypto)
- [Pay with crypto: full guide](https://teamorouter.com/blogs/pay-claude-with-usdt?utm_source=github&utm_medium=readme&utm_campaign=b6_pay-openai-api-with-crypto)
- [Create an API key](https://teamorouter.com/dashboard?tab=api-keys&utm_source=github&utm_medium=readme&utm_campaign=b6_pay-openai-api-with-crypto)

## Related guides

- [teamo-lab/claude-api-card-declined](https://github.com/teamo-lab/claude-api-card-declined): Claude API and Claude Code when Anthropic declines your card
- [teamo-lab/openrouter-usdt](https://github.com/teamo-lab/openrouter-usdt): OpenRouter only takes USDC; what to do if you hold USDT
