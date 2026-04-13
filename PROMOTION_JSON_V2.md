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
- `limits` (object)
  - `hasLimit` (boolean)
  - `detail` (string)
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
    "detail": "該結單總計簽夠4000"
  },
  "limits": {
    "hasLimit": true,
    "detail": "每曆月上限簽5000"
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
