# PortSwigger Web Security Academy: Referer-based Access Control Lab – Solved

## Lab Overview
**Difficulty:** Practitioner (feels apprentice-level once you spot it)  
**Category:** Access Control → Referer-based access control  
**Goal:** Log in as `wiener:peter` (non-admin) and exploit flawed controls to promote yourself to administrator.

App gates admin actions (like /admin-roles?username=...&action=upgrade) by checking if the Referer header points to /admin. No real role check – just trusts the header. Textbook "don't trust client-controlled data" fail.

## Exploitation Steps (How I Popped It)
1. Logged in as admin (administrator:admin) to recon the panel.
2. Went to admin panel, upgraded carlos (or any user), captured the GET /admin-roles?... request in Burp → saw Referer: https://lab-url/admin.
3. Opened incognito/private window, logged in as wiener:peter.
4. In Repeater: pasted the captured request, swapped session cookie to wiener's, changed username=carlos → username=wiener, kept the Referer header untouched.
5. Sent → 302 Found (redirect), role upgraded server-side. Logged back in → admin access confirmed. Lab solved.

(Pro tip: Without Referer, it 401s. With it forged → green light. Headers are suggestions, not gates.)

## Vulnerability Breakdown
- Access control relies on Referer header (client-supplied, forgeable).
- No server-side session/role validation on the action endpoint.
- Result: Anyone who knows/can guess the endpoint + forges Referer can escalate privileges.

## Lessons Carved Deep
- Headers lie. Always. Referer, Origin, X-Forwarded-For – all attacker-controlled.
- Traditional wisdom holds: Authenticate & authorize every request on the server. Session + role check > header trust.
- Modern fix: Ditch header-based gating. Use secure cookies, backend RBAC, maybe CSRF tokens even for GETs if needed.
- Still relevant in 2026: Companies get popped by this exact pattern. Don't be that dev.

## Tools Used
- Kali Linux in VirtualBox
- Burp Suite Professional (Repeater is undefeated)
- Firefox (incognito for clean low-priv sessions)

Another access control ghost defeated. Repo growing – next up: more broken authz or CSRF flavors? Stay grinding.

#WebSecurity #AccessControl #PortSwiggerAcademy #BugBounty #CyberSecWriteups
