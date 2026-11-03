# Security Assessment Report

**Generated:** 2026-11-03T05:21:28.063050Z

## Summary

| Metric | Count |
|--------|-------|
| Total Findings | 8 |
| CVE Vulnerabilities | 1 |
| CWE Vulnerabilities | 7 |
| Total Rules Assessed | 59 |
| Rules Passed | 52 |

### By Severity

| Severity | Count |
|----------|-------|
| mandatory | 3 |
| optional | 3 |
| potential | 2 |

## CVE Findings (Dependency Vulnerabilities)

### CVE-2024-0056: Microsoft.Data.SqlClient and System.Data.SqlClient vulnerable to SQL Data Provider Security Feature Bypass 
- **Severity:** mandatory
- **Story Points:** 1
- **Files:** packages.config:14

[CVE-2024-0056](https://github.com/advisories/GHSA-98g6-xh36-x2p7): Microsoft.Data.SqlClient and System.Data.SqlClient vulnerable to SQL Data Provider Security Feature Bypass 

Severity: HIGH

Affected dependencies:
  - Microsoft.Data.SqlClient 2.1.4 (packages.config:14; affected range < 2.1.7; first patched 2.1.7)
  - Microsoft.Data.SqlClient 2.1.4 (packages.config:14; affected range >= 3.0.0, < 3.1.5; first patched 3.1.5)
  - Microsoft.Data.SqlClient 2.1.4 (packages.config:14; affected range >= 4.0.0, < 4.0.5; first patched 4.0.5)
  - Microsoft.Data.SqlClient 2.1.4 (packages.config:14; affected range >= 5.0.0, < 5.1.3; first patched 5.1.3)

Recommended fix: Upgrade the affected package to a version outside its vulnerable range.

## CWE Findings (Code-Level Vulnerabilities)

### CWE-732: Incorrect Permission Assignment for Critical Resource
- **Category:** Credentials & Secrets
- **Severity:** optional
- **Story Points:** 5
- **Files:** Services/NotificationService.cs

NotificationService creates the private message queue and grants the Everyone principal MessageQueueAccessRights.FullControl at line 23, allowing unintended local actors to read, send, and modify queue messages.

### CWE-778: Insufficient Logging
- **Category:** Credentials & Secrets
- **Severity:** potential
- **Story Points:** 3
- **Files:** Controllers/BaseController.cs

BaseController.SendEntityNotification records create/update/delete events with the hard-coded actor name "System" (lines 24-29), despite the operations being initiated by users; the audit event therefore omits the responsible identity.

### CWE-22: Improper Limitation of a Pathname to a Restricted Directory (Path Traversal)
- **Category:** File & Path Security
- **Severity:** optional
- **Story Points:** 8
- **Files:** Controllers/CoursesController.cs

CoursesController.Edit binds TeachingMaterialImagePath from the request (line 130), then maps it with Server.MapPath and deletes the resulting file (lines 170-175) without confirming it remains under the teaching-material upload directory.

### CWE-23: Relative Path Traversal
- **Category:** File & Path Security
- **Severity:** optional
- **Story Points:** 5
- **Files:** Controllers/CoursesController.cs

CoursesController.Edit accepts TeachingMaterialImagePath from the request and passes it to Server.MapPath before deleting the resulting path (lines 130, 170-175); relative traversal segments are not rejected or constrained to the upload directory.

### CWE-434: Unrestricted Upload of File with Dangerous Type
- **Category:** File & Path Security
- **Severity:** mandatory
- **Story Points:** 8
- **Files:** Controllers/CoursesController.cs

CoursesController.Create and Edit validate only the client-supplied filename extension against an image-extension allowlist (lines 55-58 and 138-141), then save the uninspected request bytes under the web-accessible Uploads directory (lines 76-88 and 159-181). The content is not verified to be an image, so non-image content can be stored with an allowed image extension.

### CWE-99: Improper Control of Resource Identifiers ('Resource Injection')
- **Category:** Injection Attacks
- **Severity:** potential
- **Story Points:** 3
- **Files:** Controllers/CoursesController.cs

CoursesController.Edit accepts TeachingMaterialImagePath from request binding and uses it as a filesystem resource identifier via Server.MapPath before deleting the file (lines 130, 170-175).

### CWE-502: Deserialization of Untrusted Data
- **Category:** Injection Attacks
- **Severity:** mandatory
- **Story Points:** 13
- **Files:** Services/NotificationService.cs

NotificationService.ReceiveNotification reads the message body from the message queue and deserializes it into Notification with JsonConvert.DeserializeObject at line 76, without validating the resulting object. The queue is configured with Everyone FullControl at lines 20-23, so the body is not inherently trusted.
