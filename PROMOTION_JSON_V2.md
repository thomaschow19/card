# Promotion JSON v2

This repository now uses **promotion schema v2 only**. v1 has been removed.

## Top-level shape

Each promotion object has this structure:

- `schemaVersion` (number): must be `2`.
- `promotionID` (string): stable unique ID.
- `promotionName` (string): human readable title.
- `validity` (object): promotion date range.
  - `start` (ISO 8601 string)
  - `end` (ISO 8601 string)
- `eligibility` (object): all targeting rules.
  - `cardIds` (string[]): eligible card IDs from `card.json`.
  - `currencies` (object)
    - `eligible` (string[]): currencies accepted by this promotion.
    - `designatedForeign` (string[]): specific foreign currencies (optional list).
  - `channels` (string[]): normalized channels, currently `online` and/or `physical`.
  - `merchantTypes` (string[]): merchant categories/labels.
  - `paymentMethods` (string[]): payment methods/channels.
- `reward` (object)
  - `cashbackPercentage` (number)
  - `dollarPerMile` (number)
- `requirements` (object)
  - `required` (boolean)
  - `detail` (string)
  - `conditions` (array): machine-friendly requirement rules.
    - `type` (string): requirement type, e.g. `minimum_spend`, `minimum_transaction_amount`.
    - `metric` (string): evaluated metric, e.g. `spend_amount`.
    - `operator` (string): comparison operator such as `>=` or `>`.
    - `value` (number): required threshold.
    - `unit` (string): e.g. `HKD`.
    - `period` (string): e.g. `per_transaction`, `calendar_month`, `billing_cycle`.
    - `scope` (string): e.g. `per_card`, `eligible_transaction`, `issuer_portfolio`.
    - Optional fields for advanced logic: `channel`, `channelExclusion`.
- `limits` (object)
  - `hasLimit` (boolean)
  - `detail` (string)
  - `caps` (array): machine-friendly cap rules.
    - `type` (string): cap type, e.g. `maximum_eligible_spend`, `maximum_reward_amount`.
    - `metric` (string): evaluated metric, e.g. `spend_amount`, `reward_amount`.
    - `maxValue` (number): cap threshold.
    - `unit` (string): e.g. `HKD`, `HKD_equivalent`.
    - `period` (string): e.g. `calendar_month`, `calendar_quarter`, `calendar_year`, `billing_cycle`.
    - `scope` (string): e.g. `per_card`, `per_category`, `combined_campaign`.
    - Optional fields for advanced logic: `rewardComponent`.
- `registration` (object)
  - `required` (boolean)
- `notes` (string)

## Example

```json
{
  "schemaVersion": 2,
  "promotionID": "25GEN004",
  "promotionName": "SC Smart 指定商戶 5%",
  "validity": {
    "start": "2025-01-01T00:00:00Z",
    "end": "2025-12-31T00:00:00Z"
  },
  "eligibility": {
    "cardIds": ["STCSMTV"],
    "currencies": {
      "eligible": ["Hong Kong Dollar 港幣"],
      "designatedForeign": []
    },
    "channels": ["online", "physical"],
    "merchantTypes": ["Designated Merchants 指定商戶"],
    "paymentMethods": ["Apple Pay 實體", "Apple Pay 網上", "實體卡", "網上"]
  },
  "reward": {
    "cashbackPercentage": 5,
    "dollarPerMile": 0
  },
  "requirements": {
    "required": true,
    "detail": "該結單總計簽夠4000",
    "conditions": [
      {
        "type": "minimum_spend",
        "metric": "spend_amount",
        "operator": ">=",
        "value": 4000,
        "unit": "HKD",
        "period": "billing_cycle",
        "scope": "per_card_family"
      }
    ]
  },
  "limits": {
    "hasLimit": true,
    "detail": "每曆月上限簽5000",
    "caps": [
      {
        "type": "maximum_eligible_spend",
        "metric": "spend_amount",
        "maxValue": 5000,
        "unit": "HKD",
        "period": "calendar_month",
        "scope": "per_card"
      }
    ]
  },
  "registration": {
    "required": false
  },
  "notes": "4000要求包括所有smart卡簽帳"
}
```

## Notes for contributors

- Keep `promotionID` stable once published.
- Keep `schemaVersion` as `2` for all records.
- Use ISO timestamps in UTC (`...Z`) for `validity.start` and `validity.end`.
- Keep labels bilingual where possible to match current dataset style.
- Prefer normalized `eligibility.channels` values (`online`, `physical`) for code logic.
- Keep human-readable `detail` fields, but always populate numeric rule entries in `requirements.conditions` and `limits.caps` for machine processing.
