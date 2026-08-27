# MCP Tools Reference

## Email Tools

### create_mailbox
Creates a new agent email address for platform signups.

**Arguments:**
```json
{
  "domain": "mermail.to",
  "name": "research-agent"
}
```

**Returns:**
```json
{
  "email": "research-agent@mermail.to",
  "mailbox_id": "mbx-123"
}
```

### list_mailboxes
Lists all available mailboxes for the workspace.

**Arguments:** None

**Returns:**
```json
{
  "mailboxes": [
    {
      "public_id": "mbx-123",
      "email": "research-agent@mermail.to",
      "name": "Research Agent"
    }
  ]
}
```

### list_emails
Lists emails in a mailbox, with optional filters.

**Arguments:**
```json
{
  "mailboxId": "mbx-123",
  "from": "statista.com",
  "subject": "verify"
}
```

**Returns:**
```json
{
  "emails": [
    {
      "id": "msg-456",
      "from": "verify@statista.com",
      "subject": "Verify your account",
      "body": "Your code is 483912",
      "timestamp": "2026-08-27T10:00:00Z"
    }
  ]
}
```

## Wallet Tools

### get_paybox_connection
Checks the status of the PayBox connection.

**Arguments:** None

**Returns:**
```json
{
  "status": "ACTIVE",
  "console_url": "https://app.paybox.sh/..."
}
```

### paybox_request_payment
Generates a virtual card for a specific merchant.

**Arguments:**
```json
{
  "merchant": "ResearchAndMarkets",
  "merchant_url": "https://researchandmarkets.com",
  "amount_cents": 14900,
  "currency": "USD"
}
```

**Returns:**
```json
{
  "status": "success",
  "card": {
    "number": "4111111111111111",
    "expiry": "12/28",
    "cvv": "123"
  },
  "transaction_id": "txn-789"
}
```

### paybox_discover_services
Discovers available x402 services.

**Arguments:**
```json
{
  "query": "research reports"
}
```

**Returns:**
```json
{
  "services": [
    {
      "name": "ResearchAndMarkets",
      "url": "https://researchandmarkets.com",
      "type": "x402"
    }
  ]
}
```

### paybox_get_portfolio
Checks wallet balance.

**Arguments:** None

**Returns:**
```json
{
  "total": "150.00",
  "currency": "USD",
  "wallets": [
    {
      "name": "Main Wallet",
      "balance": "150.00"
    }
  ]
}
```

## Tool Usage Notes

- **Email tools** use API key authentication
- **Wallet tools** require full-profile OAuth
- **All payments** require user approval before execution
- **Virtual cards** are temporary and merchant-specific
- **Receipts** are automatically archived to inbox
