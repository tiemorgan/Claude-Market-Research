# Claude-Market-Research
GTM research on Claude offerings.

## Executive Summary — Customer Experience with Claude Units / CCU on Amazon Bedrock

**Bottom line:** Customers consuming Anthropic Claude through Amazon Bedrock today are billed in **tokens**, not in a distinct "Claude Unit." The phrase "CCU" appears in customer conversations as an informal shorthand for *consumption* on Bedrock, but Bedrock's pricing surface, APIs, and reporting are all token-denominated. This creates a generally positive but uneven customer experience: pricing is transparent and AWS-native, yet customers must do real work to translate tokens into business-relevant units of value.

### What customers experience well
- **AWS-native procurement and security.** Billing flows through the existing AWS account, IAM, CloudTrail, CloudWatch, and data-residency controls — no separate Anthropic contract is required. This is the single biggest reason enterprise customers choose Bedrock over direct API access.
- **Two clear consumption modes.** On-demand token pricing for variable workloads, and Provisioned Throughput (purchased in Model Units, hourly, with 1- and 6-month commitments) for predictable, high-volume production traffic.
- **Model choice across price/performance tiers.** Haiku, Sonnet, and Opus families let customers route workloads by ROI, and the Messages API is consistent with Anthropic's first-party API, easing portability.
- **Tooling for cost discipline.** The Count Tokens API, batch inference (up to ~50% discount), and prompt caching give cost-conscious teams concrete levers to reduce spend.

### Where the experience creates friction
- **"Claude Units / CCU" is ambiguous.** Bedrock itself does not expose a CCU SKU; the term mixes (a) Bedrock token billing, (b) Provisioned Throughput Model Units, and (c) consumption-unit constructs that exist on non-Bedrock Anthropic commercial channels. Customers frequently arrive at procurement with the wrong mental model.
- **Tokens are an engineering metric, not a business metric.** Finance and product owners must build their own mapping from tokens → requests → business outcomes (tickets resolved, documents summarized, lines of code generated) before they can forecast or chargeback.
- **Cross-model tokenizer differences.** Newer Claude versions can tokenize the same text differently, which can shift bills on model upgrades and surprise FinOps teams.
- **Provisioned Throughput economics require modeling.** Model Units are billed hourly whether used or not, and per-MU token throughput is model-dependent and not always publicly itemized — customers often need account-team guidance to size correctly.
- **Quota and capacity planning.** Default TPM quotas and regional availability sometimes require support tickets, which slows time-to-production for larger deployments.

### Typical customer journey today
1. **Discovery** — customer evaluates Claude via Bedrock for compliance, AWS billing consolidation, and model quality.
2. **POC** — builds against the Messages API; measures input/output tokens on real prompts.
3. **Cost modeling** — translates tokens into a monthly forecast and compares on-demand vs. Provisioned Throughput.
4. **Production** — deploys with IAM, observability, guardrails, and FinOps dashboards segmented by app/team/model.
5. **Optimization** — re-routes across Haiku/Sonnet/Opus, applies caching/batching, and revisits commitments quarterly.

### Recommendations to improve the customer experience
1. **Standardize the vocabulary.** In customer-facing materials, define "CCU" explicitly as Bedrock token consumption (and call out Provisioned Throughput Model Units as a separate construct) to remove procurement confusion.
2. **Ship a token-to-business-unit translation kit.** Provide a worksheet that converts tokens into cost-per-task for the top 5–10 common workloads (chat turn, doc summary, code suggestion, agent step).
3. **Lead with a sizing playbook.** Pair every pricing conversation with a decision tree for on-demand vs. Provisioned Throughput, including break-even thresholds.
4. **Pre-empt tokenizer drift.** When recommending model upgrades, include an expected token-volume delta so finance is not surprised.
5. **Bundle observability defaults.** Recommend a baseline CloudWatch + cost-allocation tagging pattern so customers can see spend by team/app/model on day one.

**Strategic read:** Customers are largely satisfied with *how* they buy Claude on Bedrock, but they are under-served on *how to think about what they're buying*. Closing that gap — clear unit definitions, business-unit translation, and pre-built sizing tools — is the highest-leverage move to improve the current CCU-on-Bedrock customer experience.

---

## AWS + Claude on Bedrock: Customer Documentation Map (Claude Units / CCU)

### 1) Core customer-facing documentation to review first

#### AWS (Bedrock)
- Amazon Bedrock pricing: https://aws.amazon.com/bedrock/pricing/
- Anthropic Claude models on Bedrock (model docs + model IDs): https://docs.aws.amazon.com/bedrock/latest/userguide/model-cards-anthropic.html
- Claude model parameters (Messages / Text Completions references): https://docs.aws.amazon.com/bedrock/latest/userguide/model-parameters-claude.html
- Provisioned Throughput (capacity planning / Model Units): https://docs.aws.amazon.com/bedrock/latest/userguide/prov-throughput.html
- Purchase Provisioned Throughput: https://docs.aws.amazon.com/bedrock/latest/userguide/prov-thru-purchase.html

#### Anthropic (using Claude on AWS Bedrock)
- Claude in Amazon Bedrock: https://docs.anthropic.com/en/docs/build-with-claude/claude-in-amazon-bedrock
- Claude API pricing (token concepts and pricing mechanics): https://docs.anthropic.com/en/docs/about-claude/pricing

> Working assumption for customer planning: Amazon Bedrock pricing is primarily token-based (input/output tokens) with optional Provisioned Throughput capacity commitments. When customers ask about “Claude Units / CCU,” confirm whether they mean Bedrock token billing vs. non-Bedrock commercial constructs.

---

## 2) Customer journey: from discovery to production

### Phase A — Understand commercial model
1. Confirm deployment channel:
   - **Bedrock-native** (AWS billing, token pricing, optional Provisioned Throughput), or
   - Other Anthropic commercial route where customer may reference “units.”
2. Pick likely model family (Haiku/Sonnet/Opus) using latency + quality needs.
3. Review pricing page with procurement/finance and establish a baseline cost envelope.

### Phase B — Technical validation (POC)
1. Enable model access in Bedrock.
2. Implement a small Messages API prototype.
3. Measure prompt + response token volumes on real use cases.
4. Capture quality, latency, and safety metrics per use case.

### Phase C — Cost model + sizing
1. Forecast monthly requests by workload.
2. Estimate average input/output tokens per request.
3. Compute cost for on-demand token billing:
   - `monthly_cost ~= (input_tokens/1M * input_rate) + (output_tokens/1M * output_rate)`
4. If load is predictable/high, compare with Provisioned Throughput economics.
5. Add scenario bands (low/base/high) and sensitivity on output-token growth.

### Phase D — Production deployment
1. Deploy with IAM, CloudWatch, and standard AWS governance controls.
2. Define quotas, failover model, and guardrails (timeouts, retries, max tokens).
3. Implement usage dashboards for:
   - token consumption by app/team/model
   - spend by environment
   - quality and latency KPIs

### Phase E — Continuous optimization
1. Re-route workloads across Haiku/Sonnet/Opus based on ROI.
2. Reduce prompt size and unnecessary context to control token burn.
3. Re-evaluate on-demand vs. Provisioned Throughput quarterly.

---

## 3) Customer lead steps (presales / solution lead motion)

1. **Qualification call**
   - Identify target workloads (chat, coding, summarization, agents, etc.).
   - Confirm compliance/data residency constraints requiring AWS Bedrock.
2. **Discovery workshop**
   - Map business process -> model capability -> latency/SLA target.
   - Define success metrics (accuracy, containment, response time, cost per task).
3. **Commercial framing**
   - Educate stakeholders on token economics and “units” terminology.
   - Align on whether “CCU” is an internal buying shorthand or formal contract unit.
4. **Pilot plan**
   - Select 1-2 high-value use cases.
   - Build token/cost baseline and expected business impact.
5. **Implementation handoff**
   - Provide architecture, IAM/security checklist, and production controls.
   - Stand up monitoring for usage, spend, and quality.
6. **Executive readout**
   - Show pilot outcomes, projected scaling cost, and recommended deployment model.
7. **Scale playbook**
   - Expand by business unit with standardized prompts, model-routing, and FinOps review cadence.

---

## 4) Practical pricing worksheet (for customer teams)

For each use case:
1. `monthly_requests`
2. `avg_input_tokens_per_request`
3. `avg_output_tokens_per_request`
4. `total_input_tokens = monthly_requests * avg_input_tokens_per_request`
5. `total_output_tokens = monthly_requests * avg_output_tokens_per_request`
6. `monthly_model_cost = (total_input_tokens/1,000,000 * input_rate) + (total_output_tokens/1,000,000 * output_rate)`
7. Add margin for retries, growth, and peak traffic (typically +10% to +30%).

This creates a repeatable and auditable path for customers to understand, implement, and calculate Claude usage economics on Bedrock.
