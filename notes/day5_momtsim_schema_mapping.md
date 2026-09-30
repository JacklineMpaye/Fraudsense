# Day 5: MoMTSim vs. Synthetic Ghana Dataset — Schema Mapping

## Direct matches

| MoMTSim          | Synthetic              | Notes                          |
|-------------------|--------------------------|----------------------------------|
| step              | step                    | Same                             |
| amount            | amount                  | Same                             |
| initiator         | customer_id             | Same role: sender ID            |
| oldBalInitiator   | old_balance             | Same                             |
| newBalInitiator   | new_balance             | Same                             |
| recipient         | recipient_id            | Same role                        |
| oldBalRecipient   | recipient_old_balance   | Same                             |
| newBalRecipient   | recipient_new_balance   | Same                             |
| transactionType   | transaction_type        | Same role, different taxonomy (see below) |
| isFraud           | isFraud                 | Same name, very different distribution (see below) |

## No MoMTSim equivalent at all
`isFlaggedFraud` (missing entirely — no rule-based pre-flag field, unlike PaySim and the synthetic dataset), `transaction_id`, `channel`, `device_type`, `network_provider`, `time_of_day`, `day_of_week`, `customer_type`, `customer_region`, `recipient_type`, `recipient_region`.

## Where they don't line up

1. **Transaction type taxonomy differs again, from both other datasets.** MoMTSim: `PAYMENT, TRANSFER, DEPOSIT, WITHDRAWAL, DEBIT`. Synthetic: `CASH_IN, CASH_OUT, TRANSFER, UTILITY_PAYMENT, AIRTIME, REVERSAL`. Only `TRANSFER` matches by name. `DEPOSIT`/`WITHDRAWAL` ≈ `CASH_IN`/`CASH_OUT` conceptually but need an explicit rename map. `AIRTIME`, `REVERSAL`, `UTILITY_PAYMENT` have no MoMTSim analog; MoMTSim's `DEBIT` has no clean home in the synthetic set.

2. **Fraud is entirely concentrated in one transaction type in MoMTSim — the key finding.** Fraud rate by type:
   - MoMTSim: `TRANSFER` = 88.9% fraud; `PAYMENT`, `DEPOSIT`, `WITHDRAWAL`, `DEBIT` = exactly 0.0% fraud each.
   - Synthetic: fraud sits at roughly 48–52% across every transaction type — no type is a giveaway.
   
   A model trained on MoMTSim could score well just by learning "is this a TRANSFER" — a shortcut unrelated to genuine behavioral fraud detection, and one that won't transfer to typology-based work. MoMTSim's apparent difficulty is structurally much lower than it looks; flag this explicitly if citing benchmark results against it.

3. **Overall fraud rate: MoMTSim 52.8%, synthetic 50% — both balanced by design.** PaySim (0.13%) is the outlier of the three, not these two.

4. **Party type is implicit in ID formatting in MoMTSim, not a column.** `recipient` values are either long numeric strings (~59%, individual-to-individual) or dash-coded like `30-0000345` (~41%, likely merchant/agent codes) — similar in spirit to PaySim's C-/M-prefix convention, but requires parsing to recover. The synthetic dataset exposes this cleanly as `customer_type`/`recipient_type` columns.

5. **No channel, device, network, time, or region context in MoMTSim** — same gap as PaySim. The synthetic dataset remains the only one of the three with that layer.

## Bottom line
MoMTSim and the synthetic dataset agree on fraud *rate* (both balanced ~50%) but disagree sharply on fraud *structure* — MoMTSim's fraud is a transaction-type tell, the synthetic dataset's isn't. That structural gap matters more for benchmarking than the column-naming differences do.