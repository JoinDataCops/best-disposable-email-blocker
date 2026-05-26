# Best disposable email blocker

Let's start with the number that breaks the marketing copy.

59 percent. That's the average detection rate across 17 disposable email services tested in an independent January 2026 benchmark. One paid service (WhoisXML) caught zero out of 16 known disposable providers. The top performer caught 16 out of 16. Zero correlation between price and accuracy.

Every vendor in this category claims 99 point something accuracy. The independent data says otherwise.

The deeper issue is that 'disposable email blocker' is the wrong frame for 2026. Static GitHub lists (the 4,000-domain disposable-email-domains repo, the 100,000-domain disposable/disposable repo) are good enough for a lot of low-ticket B2C signups. Until they aren't. Decay rate on a static list is 64 percent accuracy at one week, 43 percent at one month. And the bypasses that actually matter aren't on those lists at all. Plus addressing. Apple Hide My Email. Catch-all domains. Campaign-specific throwaway domains (Castle tracked 1,700 of those in October 2025 alone, each responsible for 400 plus abusive signup attempts).

I run signup fraud at DataCops. We've benchmarked 30 tools across the disposable-email and signup-trust category. This post is the brutally honest stack guide. Not a vendor pitch. The actual decision tree.

---

## Quick stuff people keep asking

**Are GitHub disposable email lists still useful?** Yes for the 80 percent case (low-ticket B2C, no referral abuse). Use one. Just know the decay rate. A week-old list is 64 percent accurate. A month-old list is 43 percent. Refresh weekly or pull from the API of a maintainer who refreshes daily.

**Should I block Apple Hide My Email?** No. privaterelay.appleid.com is a paying iCloud Plus user, not a disposable abuser. Blocking the TLD locks out real customers. Apple Hide My Email is a do-not-block exception, not a tempmail.

**What's the difference between deliverability tools and anti-fraud tools?** Deliverability tools (Kickbox, ZeroBounce) check whether an email will land in an inbox. Anti-fraud tools (IPQualityScore, Castle, SignUp Cops) check whether the signer-up is real. They get conflated in vendor marketing. They are not the same product.

**Is 99 percent accuracy real?** Mostly marketing. The January 2026 Prospeo benchmark of 17 services found 59 percent average against a known-disposable test set. Vendor accuracy claims do not survive independent testing.

**Should I hard-block disposable emails or soft-restrict?** Soft-restrict. Allow the signup, restrict free-trial features or quotas. Hard-blocking creates false positives that cost real customers. The big trade-off in this category.

---

## The four bypasses every static blocker misses

This is the part the listicle pages skip. Even the best static disposable-email list misses these by definition.

**Plus addressing and subaddressing.** `user+throwaway@gmail.com` reaches the same inbox as `user@gmail.com`. Most signup forms accept the plus version as a unique account. Static lists don't normalize. One real Gmail account creates infinite "unique" signups.

**Apple Hide My Email.** privaterelay.appleid.com aliases. These are real iCloud Plus users routing email through Apple's relay. They convert. They pay. Blocking the TLD blocks real customers. The static blocklists that hard-block this TLD are losing you money.

**Catch-all domains.** Anyone who owns a domain can configure a catch-all so any address `*@theirdomain.com` reaches a single inbox. Static lists don't catch random domains.

**Campaign-specific throwaway domains.** This is the Castle finding. October 2025 they tracked 1,700 domains each responsible for 400 plus abusive signup attempts. None of these were on the public lists. They were custom domains spun up for specific abuse campaigns. Static lists by definition can't catch these.

If your blocker only handles 'is this address in the disposable list', you're catching maybe 60 percent of the actual abuse and missing all four bypass classes.

---

## Tier 1: the static GitHub lists

These are free, open source, and the right starting point for a lot of low-ticket B2C use cases. They have known limits.

**1. disposable-email-domains (the 4k list, MattKetmo et al.)**

The Good: Free. Maintained for over a decade. Used by thousands of products. Fast lookup.

Frustrations: 64 percent accuracy at 1 week of staleness, 43 percent at 1 month. Bus-factor risk on solo maintainers. Doesn't normalize subaddressing. Doesn't handle Apple Hide My Email exceptions. Misses campaign-specific throwaway domains.

Wish List: Faster updates. Subaddressing normalization built in.

Value for Money: 7/10 at zero dollars. Excellent baseline.

Pricing: Free.

---

**2. disposable/disposable (the 100k list)**

The Good: Larger surface area. Catches more obscure disposable providers. Free.

Frustrations: Same decay problem. False positive rate is higher because the list is broader. Some legitimate domains have ended up on there.

Wish List: Confidence scores per domain. Faster prune cadence on false positives.

Value for Money: 7/10. Better surface, more false positives.

Pricing: Free.

---

## Tier 2: the deliverability APIs (often miscategorized)

These tools check whether an email will land. They include some disposable detection as a side effect. People reach for them because they're well-marketed.

**3. ZeroBounce**

The Good: Solid deliverability validation. Decent disposable detection on common providers. Strong reporting.

Frustrations: Built for marketing list cleanup, not signup fraud. Disposable detection misses campaign-specific throwaway domains. API costs add up at scale.

Wish List: Anti-fraud focus. Real-time signup-flow integration.

Value for Money: 7/10 for deliverability. 6/10 for fraud.

Pricing: Pay-as-you-go from $16 per 2,000 verifications.

---

**4. Kickbox**

The Good: Cleanest API in the deliverability space. Strong on bounce reduction.

Frustrations: Same deliverability vs fraud confusion. Limited bypass coverage.

Wish List: Anti-fraud product line.

Value for Money: 7/10.

Pricing: Pay-as-you-go from $0.008 per verification.

---

**5. EmailGuard**

The Good: Cheap. Decent deliverability layer. Useful for low-ticket B2C.

Frustrations: Limited fraud signal depth.

Wish List: Catch-all detection.

Value for Money: 7/10 at the price.

Pricing: From $9/mo.

---

## Tier 3: the anti-fraud APIs

These tools are built for signup-fraud, not deliverability. Detection signal is broader. Pricing is higher.

**6. IPQualityScore (IPQS)**

The Good: One of the most comprehensive risk APIs. Strong disposable detection. Good IP intelligence layer. Real-time scoring.

Frustrations: Pricing isn't friendly to sub-$5K-deal B2C. Documentation can be dense. False positive tuning takes work.

Wish List: SMB-friendly tier.

Value for Money: 8/10 enterprise. 6.5/10 SMB.

Pricing: From $99/mo, scales up fast.

---

**7. Castle**

The Good: Strong campaign-specific throwaway domain detection. Publishes the Fraudulent Email Domain Tracker monthly. Good behavioral signal layer.

Frustrations: Mid-market pricing. Setup curve is real.

Wish List: SMB tier.

Value for Money: 7.5/10.

Pricing: Quote-driven.

---

**8. SEON**

The Good: Strong identity enrichment. Social profile lookups. EU-friendly.

Frustrations: Per-API-call pricing adds up. UI is heavier than competitors.

Wish List: Lighter pricing.

Value for Money: 7/10.

Pricing: Quote.

---

**9. Sift**

The Good: Enterprise-grade fraud detection. ThreatClusters consortium model. Strong against ATO.

Frustrations: Enterprise-only. Not for SMB. Long sales cycle.

Wish List: SMB self-serve.

Value for Money: 8/10 enterprise.

Pricing: Six figures typical.

---

**10. Verisoul**

The Good: Newer entrant. Strong product-led growth. Decent SMB tier.

Frustrations: Smaller signal network than the bigger players. Brand is newer.

Wish List: More CRM integrations.

Value for Money: 7/10 SMB.

Pricing: From around $99/mo last we checked.

---

**11. Arkose Labs**

The Good: Best-in-class enterprise bot mitigation. Strong agentic AI defense.

Frustrations: Enterprise-only. Not built for the disposable-email-blocker question specifically.

Wish List: SMB tier.

Value for Money: 8/10 enterprise.

Pricing: Quote.

---

**12. FingerprintJS**

The Good: Browser fingerprinting is solid. Useful as a signal layer alongside email checks.

Frustrations: Not a disposable email blocker. Use as one layer in a stack.

Wish List: Bundled email check.

Value for Money: 7.5/10 fingerprint.

Pricing: From $80/mo.

---

**13. Castle.io, Roundtable, Rupt, SHIELD, Kount, Sardine, Onfido, Jumio, Nuvei Identity**

These play across identity verification, fraud scoring, and KYC. Most are enterprise-priced. Useful at scale, overkill for a 'disposable email blocker' question. Detailed dossiers only matter if you're already running a regulated product.

---

## Tier 4: the auth and CAPTCHA layer

These are relevant because most teams asking 'how do I block disposable emails' end up adding multiple layers. CAPTCHA and auth providers play here.

**14. Clerk, Auth0, Stytch, Frontegg, Supabase Auth, Firebase Auth, Descope, Kinde, WorkOS**

The Good: Most expose pre-signup hooks where you can plug in disposable-email checks. Clerk and Auth0 have the broadest middleware ecosystems.

Frustrations: None of them ship a serious disposable-email blocker out of the box. You bring your own list or API.

Wish List: First-class disposable-email integration in the auth flow.

Value for Money: 8/10 for auth. They aren't disposable-email blockers per se.

Pricing: Free tiers, scales with MAU.

---

**15. Cloudflare Turnstile, hCaptcha, reCAPTCHA, FunCaptcha (Arkose), GeeTest**

The Good: CAPTCHA layer adds bot friction. Cloudflare Turnstile is the most user-friendly.

Frustrations: 99.9 percent of CAPTCHAs are solved by bots in 2026 (the 'Why CAPTCHA is dead' thesis). False sense of security.

Wish List: Behavioral signal that doesn't add user friction.

Value for Money: 6/10 as a primary fraud defense. 7/10 as a friction layer.

Pricing: Mostly free, paid tiers for enterprise.

---

## Tier 5: the bundled signup-trust stack

This is the layer that bundles disposable email detection with IP intelligence, fingerprinting, and CAPI-conversion filtering. The 2026 frontier.

**16. SignUp Cops (DataCops)**

The Good: Bundles disposable email detection (160K plus fraud email domains tracked, refreshed continuously) with IP intelligence (146.4 billion datacenter IPs, 202 billion residential, 11.9 billion VPN endpoints, 620 million proxy and anonymizer IPs), browser fingerprinting (canvas, WebGL, audio, screen, fonts), and real-time risk scoring at the signup form. The branded thesis is 'why CAPTCHA is dead': humans behind the fraud, 99.9 percent of CAPTCHAs solved by bots. Replaces the reCAPTCHA plus email-verification stack with one signal pipeline. Plus, the same first-party CNAME tag that does the signup check also feeds Meta and Google CAPI, so fraudulent signups never pollute your ad-bidding training data downstream.

Frustrations: SOC 2 Type II in progress, not complete. Brand is newer than IPQualityScore or Castle. Fewer enterprise integrations than Sift or Arkose.

Wish List: Faster SOC 2. More fraud email domains beyond the 160K tracked today.

Value for Money: 8.5/10 if you want the bundle (signup fraud plus tracking plus CAPI plus consent).

Pricing: Free at 500 signup verifications, paid tiers scale up. Free tier is real.

---

## So what should you actually use?

The decision tree:

Want the simplest free baseline for low-ticket B2C? Pull the disposable-email-domains GitHub list. Refresh weekly. Add subaddressing normalization (strip everything after the plus sign). Add an Apple Hide My Email exception. That gets you 70 to 80 percent of the value at zero dollars.

Need email cleanup for marketing list deliverability? ZeroBounce or Kickbox. Don't conflate this with signup fraud.

Running a marketplace, credit-based product, or referral program where signup quality is monetary? Layer up. Static list plus IPQualityScore or Castle plus FingerprintJS. Or buy the bundled stack from DataCops or one of the other Tier 5 entrants.

Care about Apple Hide My Email being whitelisted by default? Most static lists lock out iCloud Plus users out of the box. Pick a tool that handles this exception explicitly.

Need GDPR-grade signup verification with first-party data residency? DataCops or SEON.

Already deeply embedded in Sift or Arkose at enterprise scale? Stay there. The migration cost beats the price savings.

---

## The mistake I see people make

The most common signup-fraud failure in 2026 is hard-blocking on email alone. Team installs an API that returns 'this is disposable', the form rejects it, and a percentage of real customers (paying iCloud Plus users on Apple Hide My Email, plus addressers, catch-all domain owners) get locked out at signup. Conversion drops. Revenue drops.

The fix is soft-restrict. Allow the signup. Restrict free-trial features, lower quotas, mark for manual review. Email is one signal, not a binary gate. Layer it with IP intelligence, fingerprinting, and behavioral signals. Hard-block only the highest-confidence fraud (campaign-specific throwaway domains plus a known bad IP plus a fingerprint match to a previous abuser).

---

## A few more things worth saying out loud

The bus-factor risk on solo-maintained GitHub blocklists is worth a sentence. The most popular disposable-email-domains repos have been maintained by small numbers of people for over a decade. Updates are mostly reliable. But if you're betting your signup pipeline on a single GitHub repo with one maintainer, you should mirror it locally and have a fallback. Most teams skip this and find out the hard way when an upstream PR sits unreviewed for three months and a wave of new throwaway domains slips through.

The 'is this a bot or a human-driven attack' question matters more than it used to. SignUp Cops at DataCops leans into the thesis that 99.9 percent of CAPTCHAs are solved by bots in 2026 and that the modern fraud surface is humans behind the operation, not just scripts. That changes the detection model. Fingerprinting and behavioral signals beat 'prove you're human' challenges. Don't add a CAPTCHA and call it done. The data says it's already not working.

The Apple Hide My Email exception deserves one more mention because we keep seeing teams get this wrong. privaterelay.appleid.com aliases are paying iCloud Plus subscribers. Real customers. The TechCrunch March 2026 piece on FBI obtaining identities behind iCloud aliases makes one thing clear: these are real people with real identities behind them, not anonymous fraudsters. Blocking the TLD blocks paying customers. We've seen teams lose 5 to 15 percent of conversion to this single misconfiguration.

The catch-all domain detection problem is harder than the listicles suggest. Anyone owning a domain can configure a catch-all. Real businesses do this all the time. A blanket 'is this a catch-all' check will lock out small business customers. The fix is to layer with IP intelligence, fingerprinting, and behavioral signals. Catch-all alone is not a fraud signal. Catch-all plus a known-bad IP plus a fingerprint match to a previous abuser is.

The trial-to-paid conversion gap (17.8 percent for legitimate signups vs 0.5 percent for disposable-email signups) is the line that should be on every product team's wall. The bidding model can't tell them apart unless you filter the CAPI event before it fires. The risk dashboard catching the fraud after the fact doesn't help the LTV model.

---

## Now your turn

What's your current disposable-email defense? Static list, paid API, layered stack? Have you measured the false positive rate, or are you flying blind on whether you're locking out real customers? Drop the stack and the rough numbers. The honest part of these threads is where the rest of us learn what actually works.

---

Research by [DataCops](https://www.joindatacops.com) — first-party tracking, consent infrastructure, fraud prevention, and server-side CAPI for Meta, Google, TikTok, and LinkedIn.
