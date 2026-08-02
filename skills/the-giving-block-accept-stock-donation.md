---
name: the-giving-block-accept-stock-donation
description: Accept a stock donation for a nonprofit through The Giving Block Public API — authenticate, resolve broker and ticker, price it, then create, submit, and sign the stock donation.
api: The Giving Block Public API
base_url: https://public-api.tgbwidget.com
auth: JWT bearer (Authorization: Bearer <accessToken>)
operations:
  - login
  - GetBrokersList
  - GetTickersList
  - GetTickerCost
  - CreateStockDonationPledge
  - SubmitStockDonation
  - SignStockDonation
  - GetTransactionByUuid
---

# Accept a Stock Donation

Guides an agent through The Giving Block stock donation flow. All operationIds are real
endpoints from the published API reference.

## Steps

1. **Authenticate.** `POST /v1/login` (`login`); send `Authorization: Bearer <accessToken>`
   on every call and renew via `refreshTokens` as needed.

2. **List brokers.** Call `GetBrokersList` to let the donor select their brokerage.

3. **Resolve the security.** Call `GetTickersList` to find the ticker being donated, then
   `GetTickerCost` to price the shares.

4. **Create the pledge.** Call `CreateStockDonationPledge` with the organization id, ticker,
   share quantity, and donor data.

5. **Submit the donation.** Call `SubmitStockDonation` to lodge the pledge for processing.

6. **Sign the donation.** Call `SignStockDonation` to capture the donor's authorization.

7. **Confirm.** Poll `GetTransactionByUuid` or consume the encrypted webhook to confirm
   settlement.

## Conventions & errors
- JSON in/out; success payloads are `{ data, requestId }`.
- Errors: `{ errorMessage, errorType, errorCode, meta }`; `INVALID_JWT_TOKEN` = re-auth.
- Test in the sandbox (`https://public-api.sandbox.thegivingblock.com`) with sandbox
  credentials before going live.
