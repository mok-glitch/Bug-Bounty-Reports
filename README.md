# Bug-Bounty-Reports
# Bug Report — Shopify (Sanitized Case Study)

**Title:** Stored XSS in merchant admin note rendering  
**Author:** Mokshith Kanagala (redacted)  
**Date discovered:** 2025-09-01  
**Status:** Fixed / Patched (2025-09-10)  
**Severity:** High (stored XSS affecting admin UI)

---

## Summary
A stored cross-site scripting (XSS) vulnerability was found in the merchant admin note rendering flow. A crafted note submitted via the internal notes endpoint could be stored and later executed in the admin UI when another admin viewed the note.

---

## Affected components / versions
- `admin.example-shopify-endpoint` (internal merchant admin UI)  
- Observed in merchant dashboard builds around vX.Y.Z (redacted)

---

## Impact
An attacker able to create a note (low-privilege merchant user or compromised integration) could execute arbitrary JavaScript in the context of an admin's browser, potentially allowing session token theft, actions as the admin, or UI manipulation.

---

## Reproduction (high-level, sanitized)
- Step 1: Authenticate as a merchant-level account and submit a note to the "order note" endpoint.  
- Step 2: Note is stored and later rendered in admin dashboard without sufficient output encoding.  
> **Note:** Payloads and exact API paths intentionally redacted. Do not publish PoC payloads publicly.

---

## Mitigation / Fix
Vendor remediation included:
- Proper output encoding/escaping at render time.
- Input sanitization on note creation.
- Additional CSP headers added to limit script execution in admin pages.

---

## Timeline
- 2025-09-01 — Reported to Shopify security via their bug bounty program  
- 2025-09-03 — Acknowledgement received  
- 2025-09-10 — Patch deployed to production  
- 2025-09-12 — Public, sanitized disclosure

---

## Remediation checklist (for devs)
- Encode HTML when rendering user-supplied data.  
- Apply server-side input validation.  
- Implement/strengthen Content Security Policy.  
- Consider adopting templating libraries that auto-escape.

---

## Responsible disclosure
This writeup has been sanitized. Sensitive details (payloads, exact endpoints, and PII) have been removed. Vendor permission was obtained before public disclosure.

# Bug Report — Rockstar Games (Sanitized Case Study)

**Title:** Insecure direct object reference (IDOR) in player stats export  
**Author:** Mokshith Kanagala (redacted)  
**Date discovered:** 2025-08-20  
**Status:** Fixed (2025-09-05)  
**Severity:** Medium (data exposure via insufficient authorization)

---

## Summary
An authorization gap allowed access to other users' player stats exports by modifying a numeric identifier in the export request. The backend did not sufficiently verify that the requesting session had rights to access the requested resource.

---

## Affected components / versions
- `player-stats/export` API endpoint (web portal)  
- Observed in web releases around build vA.B (redacted)

---

## Impact
An attacker able to trigger exports could access another player's exported data (non-PII gameplay stats in our case). With chained issues or additional information, impact could increase, so authorization checks are critical.

---

## Reproduction (high-level, sanitized)
- Step 1: Authenticated user requests an export for resource ID `N`.  
- Step 2: Modify the resource identifier (e.g., increment/decrement) to `N+1` and resend request. If the server lacks ownership checks, the export for another user is returned.  
> **Note:** Exact endpoints and request formats are redacted. No PoC or exploitable payloads included.

---

## Mitigation / Fix
Vendor applied:
- Strict authorization checks verifying session ownership of requested resources.  
- Rate-limiting on export endpoints.  
- Logging and alerting for repeated access attempts.

---

## Timeline
- 2025-08-20 — Reported to Rockstar security channel  
- 2025-08-22 — Acknowledged and triaged  
- 2025-09-05 — Patch rolled out to affected endpoints  
- 2025-09-07 — Public, sanitized disclosure

---

## Remediation checklist (for devs)
- Always verify resource ownership server-side (`user_id` vs. `resource.owner_id`).  
- Use opaque identifiers (UUIDs + access controls) rather than guessable sequential IDs.  
- Add logging/monitoring for abnormal export/access patterns.

---

## Responsible disclosure
This report is intentionally sanitized. Do not use the contents to attempt exploitation. Full technical details and PoC are stored in a private report (accessible to employers or vendors with permission).
