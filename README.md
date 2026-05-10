# disposable-email-toolkit

Open notes on detecting disposable, throwaway, and fraudulent email signups in 2026. Includes the four bypass classes static lists miss, the soft-restrict pattern, and integration patterns for Clerk, Auth0, and Supabase pre-signup hooks. Maintained by the team at DataCops.

## Why this exists

Independent January 2026 benchmark from Prospeo tested 17 disposable-email services against 16 known providers. Average accuracy: 59 percent. One paid service caught zero. Zero correlation between price and detection.

Vendor accuracy claims (99 percent something) do not survive independent testing. Static GitHub lists decay fast: 64 percent accuracy at one week of staleness, 43 percent at one month. And the bypasses that actually matter (subaddressing, Apple Hide My Email, catch-all domains, campaign-specific throwaway domains) are missed by static lists by definition.

This repo is the toolkit for building real signup-trust defenses in 2026.

## What's in here

- `lists/disposable-domains.txt` - merged static list refreshed daily from upstream sources
- `lists/apple-relay-exception.md` - why privaterelay.appleid.com is a do-not-block exception, not a tempmail
- `models/four-bypasses.md` - subaddressing, Apple Hide My Email, catch-all, campaign-specific throwaway
- `patterns/soft-restrict.md` - the conversion-protecting pattern: allow signup, restrict features
- `patterns/layered-detection.md` - email plus IP plus fingerprint plus behavioral signal
- `integrations/clerk-presignup-hook.md` - drop-in code for Clerk
- `integrations/auth0-action.md` - drop-in code for Auth0
- `integrations/supabase-edge-function.md` - drop-in code for Supabase
- `scripts/normalize-email.py` - subaddressing normalization (strip plus, dots in Gmail, etc.)
- `scripts/check-mx-liveness.py` - real-time MX record check (catches dead domains)
- `scripts/audit-signup-log.py` - audits 30 days of signups, classifies by likely fraud class

## The four bypasses every static blocker misses

### 1. Subaddressing and plus addressing
`user+throwaway@gmail.com` reaches the same inbox as `user@gmail.com`. Most signup forms accept the plus version as unique. Fix: normalize by stripping everything between `+` and `@` before checking against your list.

### 2. Apple Hide My Email
`privaterelay.appleid.com` aliases are paying iCloud Plus subscribers. Real customers. Blocking the TLD blocks paying users. Fix: explicit do-not-block exception. Score these as low-risk by default.

### 3. Catch-all domains
Anyone owning a domain can configure a catch-all. `*@theirdomain.com` reaches one inbox. Static lists don't catch random domains. Fix: layer with MX-liveness check and IP intelligence on the signup form session.

### 4. Campaign-specific throwaway domains
Castle's October 2025 Fraudulent Email Domain Tracker caught 1,700 of these in a single month, each responsible for 400 plus abusive signups. None on the public lists. By definition. Fix: real-time infrastructure-fingerprint detection (newly registered domain, no business activity, hosted alongside known fraud infrastructure).

## The soft-restrict pattern (the one that protects conversion)

The most common signup-fraud failure in 2026 is hard-blocking real customers. The fix:

1. Allow the signup. Don't reject at the form.
2. Score the signup with a layered model (email risk plus IP risk plus fingerprint plus behavioral).
3. Apply restrictions by score band:
   - Low risk: full free-trial access
   - Medium risk: reduced quotas, no referral credits, no payout features
   - High risk: feature gating until a payment method is added or a manual review completes
   - Very high risk plus high-confidence fraud signal: block at signup with a polite error

This pattern lets real customers through while degrading the value of fraudulent signups. False positives become a quota problem, not a lockout problem.

## Integration patterns

### Clerk pre-signup hook (Node.js)

```javascript
import { normalizeEmail, checkDisposable, scoreIP, scoreFingerprint } from './datacops-sdk';

export async function preSignupCheck(req) {
  const email = normalizeEmail(req.email);
  const emailRisk = await checkDisposable(email);
  const ipRisk = await scoreIP(req.ip);
  const fpRisk = await scoreFingerprint(req.fingerprint);

  const totalRisk = emailRisk * 0.4 + ipRisk * 0.4 + fpRisk * 0.2;

  if (totalRisk > 0.9 && emailRisk > 0.95) {
    return { allow: false, reason: 'High-confidence fraud signal' };
  }

  return { allow: true, restrictTier: scoreToTier(totalRisk) };
}
```

### Auth0 Action

Same pattern wrapped in an Auth0 Action with `event.user.app_metadata` for tier persistence.

### Supabase Edge Function

Same pattern as a Deno edge function with the SDK initialized server-side.

## When the static list is good enough

Honest answer: low-ticket B2C, no referral program, no monetary signup quality, conversion above $0 importance? Pull the disposable-email-domains GitHub list. Refresh weekly. Add subaddressing normalization. Add Apple Hide My Email exception. That's 70 to 80 percent of the value at zero dollars.

Use cases where you should layer up:
- Marketplaces (signup quality is monetary)
- Credit-based products (free credits get abused)
- Referral programs (every fake signup costs you payout)
- Regulated SaaS (fraud has compliance implications)
- Paid-media-driven SaaS (fraudulent signups pollute Meta CAPI and Smart Bidding)

## Compliance notes

These patterns assume GDPR plus TCF 2.2 plus CCPA. Signup fraud detection is anti-fraud, not profiling. It's a legitimate-interest legal basis under GDPR. Document the basis in your privacy policy.

## License

MIT. Fork it. Ship it. PRs welcome.

## About DataCops

We're a first-party trust infrastructure built on a CNAME on your own subdomain. SignUp Cops bundles disposable email detection (160K plus fraud domains tracked), IP intelligence (146.4 billion datacenter, 202 billion residential, 11.9 billion VPN endpoints, 620 million proxy and anonymizer IPs), browser fingerprinting, and real-time risk scoring. Plus first-party analytics, server-side CAPI to Meta, Google, TikTok, LinkedIn, and TCF 2.2 certified CMP. UK incorporated, Lisbon-built. SOC 2 Type II in progress (not done yet, we say so on the site). Free tier is real.

joindatacops.com

---

Research by [DataCops](https://www.joindatacops.com) · First-party tracking, consent infrastructure & fraud prevention.
