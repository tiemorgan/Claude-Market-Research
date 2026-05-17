# Claude-Market-Research
GTM research on Claude offerings.

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
