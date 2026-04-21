# Referral Program — TimkaMe v6

> Phase H.9 deliverable. Phase 2 implementation. Compiled 04/18/2026.

---

## Design

**Incentive:** $20 credit referrer + $20 credit new customer.

**Mechanics:**
- Each paying customer gets a unique referral code (auto-generated at signup)
- Code appears in: invoice emails, dashboard settings, Telegram welcome message
- New customer enters code during signup (or via referral link `timkame.com/?ref=CODE`)
- $20 applied as Stripe promo code coupon to both accounts on next invoice

## Implementation

### Stripe objects
- `Coupon`: $20 off, one-time, for referral
- `PromotionCode`: unique per customer, linked to coupon
- `Invoice`: coupon applied automatically when referral completes

### Referral tracking
```
organizations.referral_code     — unique code (e.g., "MIKHAIL20")
organizations.referred_by       — referrer's org_id (nullable)
organizations.referral_credits  — total credits earned (for display)
```

### Flow
1. Customer A shares referral link or code
2. Customer B signs up, enters code (or link auto-fills)
3. Customer B's trial starts normally
4. When Customer B pays first invoice → both A and B get $20 credit on next invoice
5. Telegram notification to A: "Your referral joined! $20 credit applied."

## Rules

- Credit only on first payment (not trial)
- No self-referral (same email domain check)
- Max 10 referral credits per month per account ($200 cap)
- Referral code does not expire
- Credits do not stack with other promotions

## Messaging (EN + RU)

**EN:**
> "Know a business owner who's tired of missing calls? Share your referral code and you both get $20 off."

**RU:**
> "Знаете владельца бизнеса, который устал пропускать звонки? Поделитесь кодом — оба получите $20 скидку."

## Metrics to Track

- Referral codes shared (link clicks)
- Referral signups (code entered)
- Referral conversions (first payment made)
- Revenue from referred customers (LTV comparison)
