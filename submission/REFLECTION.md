# Reflection — Day 17 (≤ 200 words)

Answer briefly, in your own words. This is graded on reasoning, not length.

1. **The flywheel.** Day 13 emitted agent traces; today you turned them into an
   eval set and DPO pairs that Day 22 will train on. Which step in
   `traces → Bronze → datasets` would break most silently in production if you
   got it wrong — and how would you detect it?

2. **Decontamination.** Your run dropped 2 of 3 preference pairs because their
   prompts were in the eval set. What concretely goes wrong if you *skip* this
   step and train on those pairs? How would the lie show up in your metrics?

3. **Point-in-time.** The naive join leaked a future `lifetime_spend` into the
   training row. Describe one feature in a system you know that would be
   dangerous to join without an `ASOF`/point-in-time guard.

4. **Graph vs vector.** From `kg_demo.py`, name one question the knowledge graph
   answers well that flat chunk retrieval (`embed.py`) would struggle with, and
   one where the graph is overkill.

_Write your answers below._

1. **The flywheel:** The recursive `flatten` step (span attribute extraction) breaks most silently. If telemetry key names change upstream, attributes silently resolve to `None`. We can detect this by implementing data validation contracts (e.g., Pandera) verifying that crucial columns are not fully null.
2. **Decontamination:** Skipping this causes data leakage. The model memorizes the eval answers rather than generalizing, resulting in artificially high offline evaluation metrics (like win rate) while performing poorly in production.
3. **Point-in-time:** A customer's "active subscription status" or "total spend" prior to a transaction. Joining future values leaks the target label, creating severe training-serving skew.
4. **Graph vs vector:** A multi-hop question (e.g., finding a warehouse location for a product's accessory) is answered well by KG but missed by flat vector chunks. However, simple queries like "What is the refund window?" are overkill for KG and better handled by vector retrieval.

