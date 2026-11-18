---
name: oppi-digital-products
description: Discover, sample, purchase with crypto, and unlock the Oppi digital product catalog (freelance prompt pack, Notion CRM kit, content repurposing pack). Use when a user wants ready-made AI prompts or Notion templates for freelance work, client ops, or content repurposing, and wants to buy them with crypto.
---

# Oppi Digital Products

A commerce workflow for ready-made freelance and content products: show the catalog, return a real free sample so the user can judge quality, print the correct crypto payment instructions, verify the transfer on-chain, and generate a prefilled delivery request. The skill never signs transactions, holds keys, or asks for a seed phrase; payment is a plain transfer to a published address.

## When to Use This Skill

- A user asks for ready-made AI prompts for freelance work (proposals, scope of work, client communication, delivery updates).
- A user wants a Notion CRM / project / invoice tracking template for freelancing.
- A user wants to repurpose one piece of content into social posts, emails, scripts, or SEO articles.
- A user wants to buy digital products with crypto (USDC recommended) and have delivery verified on-chain.

## What This Skill Does

1. **Catalog discovery**: Shows the Oppi digital product catalog with prices, bundles, and target audiences.
2. **Free sampling**: Returns a real free sample prompt/template from any pack so the user can test quality before paying.
3. **Crypto checkout**: Provides verified payment addresses (oppi wallet) for USDC on Solana/Base/Ethereum/BEP20/Polygon, plus BTC, TRON, LTC, DOGE.
4. **Payment verification**: Runs a read-only on-chain check of a payment transaction against the oppi wallet (public explorers/RPC, no keys).
5. **Delivery**: Starts a fulfillment request after verification so the full pack archive is delivered.

## How to Use

### Basic Usage

Run the companion MCP server (zero dependencies, Node):

```bash
npx github:Reggiebro/oppi-digital-products/mcp-server
```

Then use the tools:

1. `list_products` — show the catalog.
2. `get_free_sample` with `{"product": "ai-content-repurpose-pack"}` — test a free sample.
3. `get_payment_instructions` with `{"product": "freelance-delivery-prompt-pack"}` — get the address and amount for checkout.
4. `verify_payment` with `{"tx_hash": "...", "network": "solana"}` — confirm the payment on-chain.
5. `request_delivery` — returns a prefilled GitHub fulfillment request with tx hash, network, product, and contact.

### Examples

- "Show me what prompt packs are available and let me try a free sample."
- "I want to buy the Notion CRM kit with USDC on Base — what do I send and where?"
- "Verify my payment tx and start the delivery for the bundle."

## Notes

- Never ask for seed phrases or private keys; payment is a plain on-chain transfer to the published addresses.
- Full packs are delivered only after on-chain payment verification (manual fulfillment).
- Storefront: https://reggiebro.github.io/oppi-digital-products/ · Repo: https://github.com/Reggiebro/oppi-digital-products
