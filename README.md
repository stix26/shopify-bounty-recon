# Shopify Bug Bounty Reconnaissance

Reconnaissance pipeline output for the Shopify bug bounty program on HackerOne.

## Session: 2026-07-03

- **Subdomains enumerated:** 37,876 (shopify.com, shopifycloud.com, shopify.io)
- **Live hosts found:** 13
- **CORS findings:** 1 (GCP IAP — not exploitable)
- **Exposed files:** None confirmed
- **GitHub secrets:** None found
- **Reportable findings:** 0

## Scope

- `*.shopify.com` (max severity: medium)
- `*.shopifycloud.com` (max severity: medium)
- `*.shopify.io` (max severity: medium)
- `*.shopifycs.com` (max severity: critical)
- `*.pci.shopifyinc.com` (max severity: critical)
- Key URLs: partners.shopify.com, admin.shopify.com, accounts.shopify.com, shopify.plus, shop.app
- Source code: `github.com/Shopify/*` (max severity: medium)

## Methodology

1. Subdomain enumeration (subfinder)
2. DNS resolution + HTTP probing
3. CORS misconfiguration testing
4. Exposed file scanning (.env, .git, config, etc.)
5. GitHub commit analysis for secret leaks
6. API endpoint discovery

## Repo Structure
```
shopify-bounty-recon/
├── README.md
└── 2026-07-03/
    ├── session-log.md
    ├── targets.txt
    ├── subdomains-all.txt
    ├── resolved-hosts.txt
    ├── live-hosts.txt
    ├── cors-results.txt
    ├── exposed-files.txt
    ├── github-secrets-scan.txt
    └── all-targets.txt
```
