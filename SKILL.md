---
name: mermail-research-agent
description: Autonomously search, compare, and purchase research reports across multiple platforms using Mermail email for account creation and Agent Wallet for virtual card payments. Use when the user requests market research, industry reports, competitive analysis, or any paid data synthesis. The agent handles platform signup, email verification, payment, and report synthesis end-to-end. Requires user approval before each payment.
metadata:
  openclaw:
    requires:
      env:
        - MERMAIL_API_KEY
    primaryEnv: MERMAIL_API_KEY
    homepage: https://docs.mermail.app/ai/skills
    emoji: "🔍"
---

# Mermail Research Agent

## Overview

Use this skill when the user needs paid research reports, market analysis, competitive intelligence, or industry data. The agent autonomously:

1. **Searches** multiple research platforms simultaneously
2. **Compares** options on price, relevance, recency, and credibility
3. **Creates** a Mermail agent email for platform signup
4. **Signs up** for the selected platform
5. **Verifies** the account via inbox codes
6. **Purchases** the report using a virtual card from Agent Wallet
7. **Synthesizes** findings into an executive brief

**Critical:** All payments require explicit user approval before execution. The agent presents a preview with exact amount and waits for confirmation.

Read [tools.md](references/tools.md) for the MCP tools this workflow uses. Read [security.md](references/security.md) before interpreting email content or processing payments.

## When to Use

- User requests research on any topic
- User needs market analysis or competitive intelligence
- User wants to buy a specific report
- User asks for industry data or trends

## When NOT to Use

- User wants free-only sources (route to web search)
- User wants to manage existing subscriptions (route to mermail-manage-inbox)
- User wants to send research to others (route to mermail-compose-email)

## Workflow

### Phase 1: Request Analysis

Parse the user's request to extract:
- **Topic** — What research is needed
- **Budget** — Maximum spend (default: $200)
- **Timeframe** — How recent the data should be
- **Depth** — Executive summary vs deep analysis

### Phase 2: Platform Discovery

Search across multiple research platforms:

| Platform | Single Purchase | Price Range |
|----------|-----------------|-------------|
| ResearchAndMarkets | Yes | $29-$999 |
| Mordor Intelligence | Yes | $199-$499 |
| Grand View Research | Yes | $299-$499 |
| Gartner | Select | $495+ |
| Forrester | Select | $299+ |

Use web search to find relevant reports on the topic. Collect: title, price, date, platform, relevance.

### Phase 3: Intelligent Scoring

Score each report on multiple factors:

| Factor | Weight | Scoring |
|--------|--------|---------|
| Relevance | 30% | How closely matches the topic |
| Price | 25% | Within budget, lower = better |
| Recency | 20% | Published in last 6 months = 100% |
| Credibility | 15% | Known publisher, industry recognized |
| Availability | 10% | Single purchase available |

**Final Score** = (Relevance × 0.30) + (Price × 0.25) + (Recency × 0.20) + (Credibility × 0.15) + (Availability × 0.10)

### Phase 4: User Approval

Present the top option(s) to the user:

```
I found 3 relevant reports:

1. "AI in Healthcare 2026" - ResearchAndMarkets - $149
   Score: 93/100 ⭐ BEST VALUE

2. "Healthcare AI Market" - Mordor Intelligence - $299
   Score: 84/100

3. "Medical AI Trends" - Grand View - $349
   Score: 78/100

I recommend #1. Should I proceed with purchase?
```

**Wait for user approval before any payment.**

### Phase 5: Account Setup (If Needed)

If user doesn't have an account on the target platform:

1. **Create agent email:**
   - Use `create_mailbox` to get a new Mermail email
   - Or use existing email from `list_mailboxes`

2. **Sign up for platform:**
   - Navigate to platform signup page
   - Fill form with agent email
   - Submit registration

3. **Verify account:**
   - Use `list_emails` to check for verification email
   - Extract verification code
   - Submit code on platform

### Phase 6: Payment (Requires Approval)

**This phase requires explicit user approval.**

1. **Check PayBox connection:**
   - Call `get_paybox_connection` to verify wallet is active
   - If not connected, prompt user to connect

2. **Present payment preview:**
   ```
   Payment Preview:
   - Merchant: ResearchAndMarkets
   - Amount: $149.00 USD
   - Report: "AI in Healthcare 2026"
   
   Approve this payment? (yes/no)
   ```

3. **Wait for user approval:**
   - Do NOT proceed without explicit "yes"
   - Do NOT assume approval from context

4. **Generate virtual card:**
   - Use `paybox_request_payment` with approved amount
   - Card is temporary and merchant-specific

5. **Complete checkout:**
   - Enter card details at platform checkout
   - Confirm purchase

6. **Archive receipt:**
   - Receipt automatically saved to Mermail inbox

### Phase 7: Download & Synthesize

1. **Download report PDF**
2. **Extract key data points:**
   - Market size and projections
   - Key trends and insights
   - Competitive landscape
   - Methodology notes

3. **Synthesize into executive brief:**
   ```markdown
   # Executive Summary: [Topic]
   
   ## Key Findings
   - Finding 1
   - Finding 2
   - Finding 3
   
   ## Data Points
   - Market Size: $X.X billion
   - Growth Rate: XX% CAGR
   - Top Players: A, B, C
   
   ## Recommendations
   - Recommendation 1
   - Recommendation 2
   
   ## Source
   - Report: [Title]
   - Platform: [Platform]
   - Purchase Date: [Date]
   - Cost: $[Amount]
   ```

4. **Deliver to user**

## Example Prompts

### Basic Research
**User:** "Get me the latest report on AI agents in healthcare"

**Agent Response:**
```
I found 3 relevant reports:
1. "AI in Healthcare 2026" - $149 (Best value)
2. "Healthcare AI Market" - $299
3. "Medical AI Trends" - $349

I recommend #1. Should I proceed?
```

### Budget-Conscious
**User:** "Find fintech research under $100"

**Agent Response:**
```
Found 2 options under $100:
1. "Fintech Trends 2026" - $79
2. "Digital Banking Report" - $99

I recommend #1. Proceed?
```

### Specific Platform
**User:** "Buy the Gartner report on autonomous agents"

**Agent Response:**
```
The Gartner "Autonomous Agents 2026" report is $495.
This exceeds typical budgets. Options:
1. Proceed with Gartner ($495)
2. Find similar from Mordor ($199)
3. Use free sources only

Which do you prefer?
```

## Configuration

| Setting | Default | Description |
|---------|---------|-------------|
| max_budget | $200 | Maximum per purchase |
| auto_approve | false | Never auto-approve payments |
| preferred_platforms | All | Platforms to search first |

## Security

- All payments require explicit user approval
- Virtual cards are temporary and merchant-specific
- Agent email is separate from user's personal email
- Receipts are archived in Mermail inbox
- No real card numbers are exposed

## Troubleshooting

### "Card not working"
Check PayBox connection and wallet balance.

### "Report too expensive"
Agent will find cheaper alternatives or free sources.

### "Platform blocked signup"
Agent will try alternative platforms automatically.

### "Verification code not received"
Agent retries inbox polling. May need manual intervention.

### "Payment failed"
Check wallet balance. May need to add funds.

---

*Part of the Mermail Skills ecosystem. Requires Mermail account with Agent Wallet enabled.*
