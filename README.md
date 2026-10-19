# Pay for the OpenAI API with crypto (USDT): GPT-6, Codex and the OpenAI SDK without a card

> Maintained by the TeamoRouter team. The gateway used in this guide is ours.

OpenAI declined your card, or the API answers `403 unsupported_country_region_territory`? Call GPT models through [TeamoRouter](https://teamorouter.com/?utm_source=github&utm_medium=readme&utm_campaign=pay-openai-api-with-crypto) instead: the official OpenAI SDK talks to `api.teamorouter.com`, and you pay with USDT.

## Short answer

- **What changes in your code:** the base URL (`https://api.teamorouter.com/v1`) and the key (`sk-teamo-...`). Requests, responses and streaming stay in the standard OpenAI format.
- **How you pay:** USDT on the X Layer network from the OKX Wallet browser extension. You sign an authorization, no gas. Minimum $5.
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

Full reference: [API integration guide](https://teamorouter.com/docs/api-integration?utm_source=github&utm_medium=readme&utm_campaign=pay-openai-api-with-crypto).

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

Run `codex`. Windows (PowerShell) steps are in the [Codex CLI guide](https://teamorouter.com/docs/install-codex?utm_source=github&utm_medium=readme&utm_campaign=pay-openai-api-with-crypto). Inside a session, `/fast` toggles Fast mode.

## Top up with USDT, step by step

You need a desktop browser (Chrome, Edge or Brave) with the [OKX Wallet extension](https://chromewebstore.google.com/detail/okx-wallet/mcohilncbfahbmgdjkbpemcciiolgcge) unlocked, and USDT on the **X Layer** network in that wallet (shown on-chain as USD₮0, 1:1 with USDT). Exchange balances don't count until withdrawn to the wallet.

### 1. Get USDT onto X Layer

| Your USDT is on | Route |
|---|---|
| OKX | Assets → Withdraw → USDT → network **X Layer** → your OKX Wallet address |
| Binance | Withdraw on **Arbitrum** to your OKX Wallet address (that USDT is USDT0), then move it to X Layer on [usdt0.to](https://usdt0.to). Needs a little ETH on Arbitrum for gas |
| Another exchange | Send to your OKX account over a network both support (e.g. TRC20), then withdraw to X Layer |
| A wallet on Tron, Ethereum, Arbitrum… | Transfer page on [usdt0.to](https://usdt0.to): source = your chain, destination = X Layer, recipient = your OKX Wallet address. The page shows whether the route works; gas is paid on the source chain |

Send a small test amount first; the wrong network is the most common mistake.

### 2. Pay

1. Sign in to TeamoRouter and open the top-up dialog in the console.
2. Pick an amount ($5 minimum) and click **Pay with crypto**.
3. Approve the wallet connection, then approve switching to X Layer (the first time, adding it).
4. Sign the request: an EIP-3009 `TransferWithAuthorization` for this amount only, usable once within its validity window, with no access to anything else in your wallet. The transfer is submitted for you, so you need no OKB for gas. Some wallets show 6-decimal units: `5000000` = 5 USDT.
5. When the balance arrives, [create an API key](https://teamorouter.com/dashboard?tab=api-keys&utm_source=github&utm_medium=readme&utm_campaign=pay-openai-api-with-crypto).

Full walkthrough: [Pay for Claude and GPT APIs with USDT on X Layer](https://teamorouter.com/blogs/pay-claude-with-usdt?utm_source=github&utm_medium=readme&utm_campaign=pay-openai-api-with-crypto).

## Common errors

| You see | Fix |
|---|---|
| No wallet found | Install and unlock OKX Wallet, reload. Use a desktop browser; mobile ones usually have no wallet extension |
| `insufficient_balance` when paying | Not enough USD₮0 **on X Layer**. USDT on other chains or on an exchange doesn't count |
| Code `-32603` | Unlock OKX Wallet, disable other wallet extensions such as MetaMask, reload |
| API `401` | Key copied incompletely or deleted in the console |
| API `402 insufficient_balance` | Balance can't cover the request; long-context requests reserve their maximum cost first |
| Codex `404 … url: https://api.teamorouter.com/responses` | `/v1` is missing from `base_url` |

## FAQ

**Do I need an OpenAI account or ChatGPT Plus?** No. You call `api.teamorouter.com` with a TeamoRouter key and pay only for tokens.

**Do Codex usage limits apply?** The limits that reset every few hours or weekly belong to ChatGPT plans. With an API key you pay per token as long as you have balance.

**Can the same key call Claude?** Yes, on the Anthropic native protocol: the Anthropic SDK with `base_url="https://api.teamorouter.com"` (no `/v1`), or [Claude Code](https://teamorouter.com/docs/install-claude-code?utm_source=github&utm_medium=readme&utm_campaign=pay-openai-api-with-crypto) via `ANTHROPIC_BASE_URL`. Through the OpenAI format, Claude can lose prompt caching and thinking.

**What does it cost?** Per-token prices for each model are on the [pricing page](https://teamorouter.com/?utm_source=github&utm_medium=readme&utm_campaign=pay-openai-api-with-crypto#pricing). No subscription.

## Links

- [TeamoRouter home](https://teamorouter.com/?utm_source=github&utm_medium=readme&utm_campaign=pay-openai-api-with-crypto)
- [Pay with USDT: full guide](https://teamorouter.com/blogs/pay-claude-with-usdt?utm_source=github&utm_medium=readme&utm_campaign=pay-openai-api-with-crypto)
- [Create an API key](https://teamorouter.com/dashboard?tab=api-keys&utm_source=github&utm_medium=readme&utm_campaign=pay-openai-api-with-crypto)
