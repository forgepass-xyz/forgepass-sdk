# forgepass-sdk

**The ForgePass client SDK — integrate contributor reputation into any Stellar project**

This SDK is how external Stellar projects consume ForgePass data without writing API integration code from scratch. It provides typed, versioned wrappers around the ForgePass public API, making it straightforward to add contributor Trust Scores, passport lookups, and badge verification to any Stellar application.

> Part of the [ForgePass](https://github.com/forgepass-xyz) open-source ecosystem.

---

## What This SDK Does

ForgePass exposes a public REST and GraphQL API — free to use by any Stellar project. This SDK wraps that API with a clean TypeScript interface so developers can integrate reputation data in minutes rather than hours.

If you are building a Stellar project and want to:

- **Rank contributors** by Trust Score when they apply to your campaign or bounty
- **Surface a contributor's verified history** alongside their application or profile
- **Weight governance votes** using contribution history in addition to token balance
- **Verify a contributor's credentials** before assigning work or granting access

...this SDK is where you start.

---

## Installation

```bash
npm install @forgepass/sdk
```

---

## Quick Start

```typescript
import { ForgePass } from '@forgepass/sdk';

const fp = new ForgePass();

// Look up a contributor's full passport
const passport = await fp.getPassport('GCONTRIBUTOR...STELLAR_ADDRESS');

// Get just their Trust Score
const score = await fp.getTrustScore('GCONTRIBUTOR...STELLAR_ADDRESS');
console.log(score.total);         // Overall score
console.log(score.breakdown);     // Per-signal breakdown

// Check which achievement badges they hold
const badges = await fp.getBadges('GCONTRIBUTOR...STELLAR_ADDRESS');

// Query multiple contributors and sort by score
const contributors = await fp.listContributors({
  minScore: 40,
  sortBy: 'trustScore',
  limit: 20,
});
```

---

## Core Methods

| Method | What It Returns |
|---|---|
| `getPassport(address)` | Full passport record — credentials, score, badges, contribution history |
| `getTrustScore(address)` | Trust Score with per-signal breakdown |
| `getBadges(address)` | List of soulbound achievement badges |
| `getContributorGraph(address)` | Collaborator network — who they've shipped with |
| `listContributors(filters)` | Filtered, paginated contributor index |
| `verifyCredential(address, type)` | Check whether a contributor holds a specific credential |

Full method reference: [forgepass-docs](../forgepass-docs)

---

## Example Integration: Ranking Applicants

```typescript
import { ForgePass } from '@forgepass/sdk';

const fp = new ForgePass();

async function rankApplicants(applicantAddresses: string[]) {
  const scored = await Promise.all(
    applicantAddresses.map(async (address) => ({
      address,
      score: await fp.getTrustScore(address),
    }))
  );

  return scored.sort((a, b) => b.score.total - a.score.total);
}
```

---

## Tech Stack

- **Language:** TypeScript
- **Runtime:** Node.js / browser-compatible
- **Depends on:** [`forgepass-api`](../forgepass-api) public endpoints

No Stellar SDK dependency required for read-only use. The SDK communicates with the ForgePass API — you do not need to run a Horizon node or interact with Soroban directly.

---

## Development Setup

```bash
git clone https://github.com/forgepass-xyz/forgepass-sdk
cd forgepass-sdk
npm install
npm run build
npm test
```

To point the SDK at a local API instance during development:

```typescript
const fp = new ForgePass({ apiUrl: 'http://localhost:3001' });
```

---

## Relationship to Other Repos

- Makes calls to **[`forgepass-api`](../forgepass-api)** endpoints.
- Optionally surfaces data from **[`forgepass-contracts`](../forgepass-contracts)** for projects that need direct on-chain verification.

---

## Contributing

This is a great entry point for TypeScript developers new to ForgePass — the surface area is small and well-defined. Issues are labelled `good-first-issue`.

All code is **MIT licensed**.