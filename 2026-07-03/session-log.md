# Shopify Bug Bounty Recon - July 3, 2026

**Target:** Shopify (HackerOne handle: `shopify`)  
**Duration:** 1 session  
**Status:** No reportable findings

---

## Scope Summary

- **Eligible scopes:** 19 (wildcards + URLs + source code)
- **Key wildcards:** `*.shopify.com`, `*.shopifycloud.com`, `*.shopify.io`, `*.shopifycs.com`, `*.pci.shopifyinc.com`
- **Key URLs:** partners.shopify.com, shopify.plus, shop.app, linkpop.com, admin.shopify.com, accounts.shopify.com
- **Source code:** `github.com/Shopify/*`
- **Bounties:** Yes | **State:** Public, open for submissions

---

## Recon Pipeline

### 1. Subdomain Enumeration (subfinder)

| Domain | Subdomains Found |
|--------|-----------------|
| shopify.com | 25,057 |
| shopifycloud.com | 11,966 |
| shopify.io | 853 |
| **Total unique** | **37,876** |

### 2. Live Host Probing

- 37,876 subdomains checked for DNS resolution → **38 resolved**
- HTTP probing of resolved hosts → **13 live hosts** (2xx/3xx)
- Additional key URL targets probed separately

**Live hosts:**
- academy.shopify.com (301)
- acceleration.shopify.com (301)
- accio-staging.shopify.io (302)
- accounts-staging.shopify.io (200)
- accio.shopify.io (302)
- admin-sitemap.shopify.io (302, GCP IAP)
- adequate-desk.shopify.io (301)
- ads-bidify.shopify.io (302)
- ads-bidify-state-manager.shopify.io (302)
- all-editions.shopify.com (302)
- angry-owl.shopify.io (302)
- antoniam.shopify.io (302)
- antoniam-staging.shopify.io (302)

**Key scope URLs (all 200 except where noted):**
- partners.shopify.com (200)
- shopify.plus (200)
- linkpop.com (200)
- www.shopify.com (200)
- shop.app (403)
- admin.shopify.com (403)
- accounts.shopify.com (403)
- arrive-server.shopifycloud.com (200)
- your-store.myshopify.com (200)

### 3. CORS Testing

- Tested all live hosts with `Origin: https://evil.com` and `Origin: null`
- **admin-sitemap.shopify.io** reflected arbitrary Origin with `Access-Control-Allow-Credentials: true`
  - Mitigating factor: Behind **Google Cloud IAP** (Identity-Aware Proxy). The CORS headers are on the 302 redirect to Google's OAuth endpoint, set by GCP infrastructure for OAuth flow, not by the Shopify application
  - Response: `"Invalid IAP credentials: empty token"` — no authenticated content accessible
  - **Verdict: Not exploitable** — GCP IAP expected behavior, no sensitive data exposed
- All other targets: No CORS headers returned

### 4. Exposed File Scanning

- Tested 19 paths on 6 key targets (partners.shopify.com, shopify.plus, linkpop.com, www.shopify.com, your-store.myshopify.com, arrive-server.shopifycloud.com)
- **False positives (catch-all 200 pages):** shopify.plus (all paths return 745KB HTML), your-store.myshopify.com (all paths return 42KB HTML)
- **Legitimate files:** www.shopify.com/robots.txt, www.shopify.com/sitemap.xml, www.shopify.com/.well-known/security.txt
- **No exposed secrets, .env, .git, or database dumps confirmed**

### 5. GitHub Secret Scanning

- Scanned recent commits on top 8 Shopify repos (liquid, react-native-skia, dawn, hydrogen, dashing, cli, ruby-lsp, shopify-frontend-template-react)
- **No secret leaks found** in recent commit history
- Only general security-related commit messages (auth refactors, CVEs)

### 6. API Discovery

- Checked for GraphQL, Swagger, OpenAPI on www.shopify.com, partners.shopify.com, linkpop.com, shopify.plus
- **None found** (all returned 404 or catch-all 200)

---

## Summary

| Category | Result |
|----------|--------|
| CORS | 1 finding — GCP IAP (not exploitable) |
| Exposed files | None confirmed (all catch-all pages) |
| GitHub secrets | None in recent commits |
| API endpoints | None discovered |
| **Verdict** | **No reportable findings this session** |

## Notes

- Most Shopify infrastructure is behind Cloudflare/WAF or requires authentication
- admin.shopify.com and accounts.shopify.com return 403 without auth cookies
- shop.app returns 403 (likely WAF/CAPTCHA)
- Massive subdomain count (37k) but very few resolve — Shopify uses wildcard DNS and merchant-specific routing
- GCP IAP on admin-sitemap.shopify.io confirms Shopify uses Google Cloud for internal tooling
