# @three-ws/solana-memo-media-mcp

<!-- three.ws:badges -->
[![GitHub stars](https://img.shields.io/github/stars/nirholas/solana-memo-media-mcp?style=flat&logo=github)](https://github.com/nirholas/solana-memo-media-mcp/stargazers) [![License](https://img.shields.io/github/license/nirholas/solana-memo-media-mcp?style=flat)](https://github.com/nirholas/solana-memo-media-mcp/blob/HEAD/LICENSE) [![Last commit](https://img.shields.io/github/last-commit/nirholas/solana-memo-media-mcp?style=flat)](https://github.com/nirholas/solana-memo-media-mcp/commits) [![PRs welcome](https://img.shields.io/badge/PRs-welcome-brightgreen?style=flat)](https://github.com/nirholas/solana-memo-media-mcp/pulls) [![AI agent friendly](https://img.shields.io/badge/AI%20agents-AGENTS.md%20%2B%20llms.txt-6d5dfc?style=flat)](https://github.com/nirholas/solana-memo-media-mcp/blob/HEAD/AGENTS.md)
<!-- /three.ws:badges -->


Read and display image data URIs embedded in Solana SPL Memo instructions. Instead of copying a `data:image/...;base64,...` memo payload into a browser converter, give an AI assistant the transaction signature and receive the validated image inline.

This is a keyless, read-only stdio MCP server. It never signs, sends, uploads, caches, or modifies chain data.

## Install

```bash
claude mcp add solana-memo-media -- npx -y @three-ws/solana-memo-media-mcp
```

For Cursor, Claude Desktop, or another stdio MCP client:

```json
{
  "mcpServers": {
    "solana-memo-media": {
      "command": "npx",
      "args": ["-y", "@three-ws/solana-memo-media-mcp"]
    }
  }
}
```

## Tools

| Tool | Purpose |
| --- | --- |
| `extract_solana_memo_media` | Fetch one legacy, v0, or v1 transaction and return every supported image in its top-level and inner Memo instructions, with signer, slot, and version. |
| `find_solana_memo_media` | Scan up to 1,000 recent signatures for an address (pageable with `before`). Only signatures whose memo summary holds a data URI are fetched, so a scan costs a handful of RPC calls. |
| `decode_solana_memo_data_uri` | Decode one already-copied data URI locally. No network call. |
| `get_solana_memo_media_status` | Show the local policy, recognized memo programs, and RPC failover hosts. No network call. |

Recognized memo programs: SPL Memo v1 (`Memo1UhkJRfHyvLMcVLMbLJYfW2tfU8G8H4PqvV6v8`), SPL Memo v2 (`MemoSq4gqABAXKb96qnH8TysNcWxMyWCqXgDLGmfcHr`), and the newer program carried by version 1 transactions (`Memo4c2pN8afCj432Lb7RMVKi9PbQnnW7ewFFaV3oAH`).

## Example

Ask an MCP client: "Show any image embedded in Solana transaction `<signature>`." It calls `extract_solana_memo_media` with:

```json
{ "signature": "<base58 Solana transaction signature>" }
```

The response is a JSON record plus one MCP `image` content block per asset. The record carries the signature, fee payer, slot, transaction version, and for each asset its MIME type, byte length, SHA-256, and dimensions. The raw data URI is intentionally not echoed into text output.

Use it from your own code:

```js
import { decodeDataUri } from '@three-ws/solana-memo-media-mcp/src/lib/media.js';

const media = decodeDataUri('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVR42mP8z8BQDwAFgwJ/lwV8YQAAAABJRU5ErkJggg==');
console.log(media.mimeType, media.byteLength, media.dimensions, media.sha256);
```

Full guide: [docs/mcp-solana-memo-media.md](../../docs/mcp-solana-memo-media.md).

## Safety and provenance

An SPL Memo is arbitrary signed text. A data URI is only a convention inside that text, not a Solana media standard and not an endorsement by a wallet, explorer, or this package.

- Only `image/png`, `image/jpeg`, `image/webp`, and `image/gif` are accepted.
- SVG and all executable or document media are rejected.
- Decoded content is capped at 262,144 bytes.
- The declared MIME type must match PNG, JPEG, WebP, or GIF file signatures before an image is returned.
- The server reports a SHA-256 digest so callers can preserve an exact reference.
- Verify the transaction signer, program, and surrounding transaction context independently before trusting what an image means.

## RPC configuration

The server tries `SOLANA_RPC_URLS`, then `SOLANA_RPC_URL`, then public Solana mainnet endpoints. You do not need a key for normal use.

| Variable | Required | Meaning |
| --- | --- | --- |
| `SOLANA_RPC_URLS` | no | Comma-separated ordered JSON-RPC failover endpoints. |
| `SOLANA_RPC_URL` | no | One primary JSON-RPC endpoint. |

## Development

```bash
npm test --prefix packages/solana-memo-media-mcp
npx -y @modelcontextprotocol/inspector node packages/solana-memo-media-mcp/src/index.js
```

License: Apache-2.0.

<!-- three.ws:growth -->
## Support the project

If solana-memo-media-mcp saves you time, **[star it on GitHub](https://github.com/nirholas/solana-memo-media-mcp)**. Stars are how other developers and AI agents find the repositories worth trusting, and they cost you one click.

Know someone who would use it? [Post on X](https://twitter.com/intent/tweet?text=solana-memo-media-mcp%3A%20Extract%2C%20validate%2C%20and%20display%20image%20data%20URIs%20embedded%20in%20Solana%20SPL%20Memo%20instructions&url=https%3A%2F%2Fgithub.com%2Fnirholas%2Fsolana-memo-media-mcp) · [Share on Bluesky](https://bsky.app/intent/compose?text=solana-memo-media-mcp%3A%20Extract%2C%20validate%2C%20and%20display%20image%20data%20URIs%20embedded%20in%20Solana%20SPL%20Memo%20instructions%20https%3A%2F%2Fgithub.com%2Fnirholas%2Fsolana-memo-media-mcp) · [Share on LinkedIn](https://www.linkedin.com/sharing/share-offsite/?url=https%3A%2F%2Fgithub.com%2Fnirholas%2Fsolana-memo-media-mcp) · [Submit to Hacker News](https://news.ycombinator.com/submitlink?u=https%3A%2F%2Fgithub.com%2Fnirholas%2Fsolana-memo-media-mcp&t=solana-memo-media-mcp%3A%20Extract%2C%20validate%2C%20and%20display%20image%20data%20URIs%20embedded%20in%20Solana%20SPL%20Memo%20instructions) · [Share on Reddit](https://www.reddit.com/submit?url=https%3A%2F%2Fgithub.com%2Fnirholas%2Fsolana-memo-media-mcp&title=solana-memo-media-mcp%3A%20Extract%2C%20validate%2C%20and%20display%20image%20data%20URIs%20embedded%20in%20Solana%20SPL%20Memo%20instructions)

## Built for AI agents too

Coding agents and LLM tooling can read this repo directly: [AGENTS.md](./AGENTS.md), [llms.txt](./llms.txt), [llms-full.txt](./llms-full.txt). Point an agent at `https://github.com/nirholas/solana-memo-media-mcp` and it has the context it needs.

## More from the same author

- [All repositories by nirholas](https://github.com/nirholas/nirholas#readme): the full catalog, grouped by topic
- [three.ws](https://three.ws): the platform for 3D AI agents with Solana wallets, a skill marketplace and x402 payments
- Questions or ideas: [open an issue](https://github.com/nirholas/solana-memo-media-mcp/issues) or [start a discussion](https://github.com/nirholas/solana-memo-media-mcp/discussions)

## Contributors

[![Contributors](https://contrib.rocks/image?repo=nirholas/solana-memo-media-mcp)](https://github.com/nirholas/solana-memo-media-mcp/graphs/contributors)

## Star history

[![Star History Chart](https://api.star-history.com/svg?repos=nirholas/solana-memo-media-mcp&type=Date)](https://www.star-history.com/#nirholas/solana-memo-media-mcp&Date)

<!-- /three.ws:growth -->

<!-- three.ws:ecosystem -->
## Part of three.ws

[three.ws](https://three.ws) is a platform for 3D AI agents with Solana wallets: avatars, a skill marketplace, x402 payments and more than seventy MCP servers. `@three-ws/solana-memo-media-mcp` is one package from it.

- Documentation site: https://nirholas.github.io/solana-memo-media-mcp/
- npm: https://www.npmjs.com/package/@three-ws/solana-memo-media-mcp
- Canonical source: https://github.com/nirholas/three.ws (this repository is a generated mirror of `packages/solana-memo-media-mcp`)
- Agent-readable summary: [llms.txt](./llms.txt) and [AGENTS.md](./AGENTS.md)
- Issues and ideas: https://github.com/nirholas/solana-memo-media-mcp/issues
<!-- /three.ws:ecosystem -->
