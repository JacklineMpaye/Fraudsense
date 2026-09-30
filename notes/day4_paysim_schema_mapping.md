# Day 4: PaySim vs. Synthetic Ghana Dataset — Schema Mapping

## Direct matches (9 of PaySim's 11 columns)

| PaySim            | Synthetic              | Notes                          |
|--------------------|-------------------------|----------------------------------|
| step               | step                    | Same — time unit                |
| amount             | amount                  | Same                             |
| nameOrig           | customer_id             | Same role: sender ID            |
| oldbalanceOrg      | old_balance             | Same                             |
| newbalanceOrig     | new_balance             | Same                             |
| nameDest           | recipient_id            | Same role: receiver ID          |
| oldbalanceDest     | recipient_old_balance   | Same                             |
| newbalanceDest     | recipient_new_balance   | Same                             |
| type               | transaction_type        | Same role, different taxonomy (see below) |

## Where they don't line up

1. **Transaction type taxonomy differs.** PaySim: `CASH_IN, CASH_OUT, DEBIT, PAYMENT, TRANSFER`. Synthetic: `CASH_IN, CASH_OUT, TRANSFER, UTILITY_PAYMENT, AIRTIME, REVERSAL`. `AIRTIME` and `REVERSAL` have no PaySim equivalent; PaySim's `PAYMENT`/`DEBIT` don't map cleanly onto `UTILITY_PAYMENT`. Needs reconciling before any cross-dataset model comparison.

2. **`isFraud` distributions are opposite by design.** PaySim: ~0.13% fraud (realistic imbalance). Synthetic: 50% fraud (balanced by construction). Raw accuracy/F1 aren't comparable across the two without resampling one side — lean on precision/recall/AUC or typology-level comparison instead.

3. **`isFlaggedFraud` means something different in each.** PaySim: a simple rule (transfers > 200,000), almost always 0. Synthetic: fires on 40% of rows — a very different rule generated it. Check the synthetic generator's flagging logic before treating these as comparable.

4. **PaySim has no identity/context beyond two ID strings.** Synthetic adds `customer_type`/`recipient_type` (Agent, Government, Merchant, Individual) and `customer_region`/`recipient_region` (9 Ghana regions) — enables typology signals ("Agent → Individual, Northern Region") PaySim can't represent.

5. **No behavioral/channel context in PaySim at all.** `channel` (USSD/API/Agent/App), `device_type` (iOS/FeaturePhone/Android), `network_provider` (MTN/Vodafone/AirtelTigo), `time_of_day`, `day_of_week` — all pure additions in the synthetic set.

## Bottom line
Structurally close (9/11 fields map directly), but PaySim is a stripped-down, class-imbalanced, identity-blind proxy. The synthetic dataset is richer and class-balanced by design. PaySim benchmarking will show how the model handles a different fraud rate and narrower feature set — not whether the typologies themselves are "correct," since PaySim can't express most of them.