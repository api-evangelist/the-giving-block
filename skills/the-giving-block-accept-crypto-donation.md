---
name: the-giving-block-accept-crypto-donation
description: Accept a cryptocurrency donation for a nonprofit through The Giving Block Public API — authenticate, look up supported currencies and rate, create a deposit address, and confirm settlement.
api: The Giving Block Public API
base_url: https://public-api.tgbwidget.com
auth: JWT bearer (Authorization: Bearer <accessToken>)
operations:
  - login
  - GetOrganizationsList
  - GetCurrencyListV2
  - GetCryptoToUSDRate
  - CreateDepositAddress
  - GetTransactionByUuid
---

# Accept a Crypto Donation

Guides an agent through The Giving Block crypto donation flow. All operationIds below are
real endpoints from the published API reference. Never invent test values; request sandbox
credentials for testing against `https://public-api.sandbox.thegivingblock.com`.

## Prerequisites
- API user credentials issued by The Giving Block (email their integrations team).
- (Optional) AES webhook keys if you will consume webhook confirmations.

## Steps

1. **Authenticate.** `POST /v1/login` (operation `login`) with your issued credentials.
   Store the returned access token and refresh token. Send the access token as
   `Authorization: Bearer <accessToken>` on every subsequent call. Renew with
   `refreshTokens` (`POST /v1/refresh-tokens`) when it expires — a stale token returns
   `errorType: INVALID_JWT_TOKEN` (HTTP 401).

2. **Resolve the organization.** Call `GetOrganizationsList` to find the target nonprofit's
   organization id, or use a known id directly.

3. **List supported currencies.** Call `GetCurrencyListV2` (the current list; `V1` is
   superseded) to pick the cryptocurrency the donor wants to give.

4. **Quote the value (optional).** Call `GetCryptoToUSDRate` to show the donor the current
   USD equivalent.

5. **Create the deposit address.** Call `CreateDepositAddress` with the organization id,
   pledge amount, currency, and donor data. The response includes a crypto deposit address
   and a QR code. Present these to the donor to send funds.

6. **Confirm settlement.** Either consume the AES-256-encrypted webhook notification (see
   `webhooks/the-giving-block-webhooks.yml`) or poll `GetTransactionByUuid` to confirm the
   donation was received and converted.

## Conventions & errors
- Requests/responses are JSON; success payloads wrap data in `{ data, requestId }`.
- Errors use `{ errorMessage, errorType, errorCode, meta }` (not RFC 9457).
- No idempotency-key is documented; avoid blindly retrying `CreateDepositAddress`.
