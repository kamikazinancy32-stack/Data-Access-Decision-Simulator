# Data Access Decision Simulator

**Role:** DevOps Engineer, EduConnect Ghana **Scope:** Three data access requests received Monday morning **Policy references:** EduConnect Data Classification Policy (PUBLIC / INTERNAL / CONFIDENTIAL), Principle of Least Privilege, Data Lifecycle (Create → Store → Use → Share → Archive → Destroy), Ghana Data Protection Act, 2012 (Act 843\)

---

## Classification Reference

| Tier | Examples | Who can access |
| :---- | :---- | :---- |
| PUBLIC | Marketing materials, public course catalog | Anyone |
| INTERNAL | Aggregated analytics, internal reports, names, enrollments | Staff with a business need |
| CONFIDENTIAL | Student PII (emails, phone numbers, profiles), grades, payment info | Named roles only, with approval |

> **Note on gaps in the policy:** Phone numbers are not listed in the classification for Request 1\. Because they are personal contact details that identify a student, they are treated as **CONFIDENTIAL** in this review. I recommend the data owner updates the policy to say this explicitly.

---

## Request 1: Marketing Campaign

**Requester:** Alizah, Marketing Manager **Asked for:** Full student database (names, emails, phone numbers, course enrollments) for a referral campaign starting Friday **Requester's access level:** INTERNAL

### Data Access Decision Form

| Field | Response |
| :---- | :---- |
| **Decision** | **Conditionally Approve** (the full database export is denied; a limited, controlled alternative is approved) |
| **Lifecycle Stage** | **Use**, with a risk of moving into **Share** if the data is exported to an outside email or SMS tool |
| **Data requested vs. needed** | Names (INTERNAL): needed. Enrollments (INTERNAL): needed for targeting. Emails (CONFIDENTIAL): needed to contact students, but Alizah does not need to *see* them. Phone numbers (CONFIDENTIAL): not needed for an email referral campaign. |

### Justification

1. **Classification policy:** Alizah holds INTERNAL access. Emails and phone numbers are CONFIDENTIAL, so granting them directly would put her above her approved level.  
2. **Least privilege:** The goal is to *send* a referral message, not to *hold* a copy of every student's contact details. Marketing needs the campaign to reach students, not the raw database. Phone numbers add risk and give no benefit for this campaign.  
3. **Purpose limitation (Act 843, Section 17 principles):** Contact details were collected to deliver courses and support. Using them for marketing is a new purpose. Only students who agreed to receive marketing messages should be contacted, and every message must allow them to opt out.  
4. **Urgency is not a control:** A Friday deadline does not change the classification of the data. A fast, safe alternative exists (below), so the campaign does not need to be delayed.

### Conditions / Safeguards

- No raw export of the student database to Alizah or the marketing team.  
- Phone numbers are excluded entirely.  
- The campaign is sent **through the platform's approved email system**. Marketing provides the message and the audience rules (e.g. "students enrolled in X"). Engineering runs the send. Alizah sees counts and results, not email addresses.  
- Only students who have **opted in to marketing** are included. Everyone else is filtered out.  
- Every email includes a working **unsubscribe link**, and opt-outs are honoured immediately.  
- If any temporary list must be created, it is encrypted, access is time-limited (expires after the campaign), and the list is deleted when the campaign ends.  
- All access and sends are logged.

### Who Must Be Consulted

- **Data Protection Officer (DPO) / Privacy Lead:** confirm the marketing purpose and consent basis  
- **Data Owner** (e.g. Head of Student Records): approve use of CONFIDENTIAL fields  
- **Information Security:** approve the send method and logging  
- **Alizah's manager:** confirm the business need

### Action Steps

1. Reply to Alizah the same day: explain the full export is not possible, and offer the controlled send so the Friday launch stays on track.  
2. Ask the DPO to confirm the consent basis and which students have opted in.  
3. Build the audience filter (enrolled \+ opted in) inside the platform. No export.  
4. Marketing supplies the email content, including the unsubscribe link. Review it before sending.  
5. Run the send through the approved email system and record it in the ticket.  
6. Share aggregate results (sent, opened, referrals) with Alizah.  
7. Close the ticket with the decision, approvers and safeguards recorded.

---

## Request 2: Analytics Partnership

**Requester:** Chris, Head of Product **Asked for:** Direct access to the production AWS database for DataInsights Inc. (US-based) to analyse learning patterns. Login credentials were attached to the ticket. **Data involved:** Student activity logs (INTERNAL), Student profiles (CONFIDENTIAL) **Compliance:** EduConnect is subject to Ghana's DPA (Act 843\)

### Data Access Decision Form

| Field | Response |
| :---- | :---- |
| **Decision** | **Deny** (as requested). A redesigned, compliant data share can be considered once the documentation below is in place. |
| **Lifecycle Stage** | **Share** (data leaving EduConnect to an external third party, across borders) |

### Legal and Compliance Red Flags

| \# | Red flag | Why it matters |
| :---- | :---- | :---- |
| 1 | **Credentials attached to a ticket** | Credentials are now exposed to anyone who can read the ticket. This is a security incident in itself. Shared logins also break accountability: we can't tell who did what. |
| 2 | **Direct access to the production database** | Gives the partner far more than they need, including every CONFIDENTIAL table. This is the opposite of least privilege. |
| 3 | **CONFIDENTIAL student profiles included** | Pattern analysis does not require names, emails or other identifying details. |
| 4 | **Cross-border transfer to the US** | Personal data of Ghanaian students would be processed outside Ghana. EduConnect stays responsible for that data under Act 843 and must make sure the partner protects it to the same standard. |
| 5 | **No contract or processing agreement mentioned** | Under Act 843, a data controller that uses a processor must make sure the processor applies proper security measures, set out in a written agreement. |
| 6 | **Partnership signed without Legal / DPO review** | A business agreement was made before the privacy and security impact was assessed. |
| 7 | **Students not informed** | Students were not told their data would be shared with an outside analytics company. This conflicts with the openness and purpose principles in Act 843\. |

### Justification

- **Classification policy:** Student profiles are CONFIDENTIAL and cannot be given to an external party without formal approval and controls.  
- **Least privilege:** The partner needs learning *patterns*, not identities. Full database access is excessive.  
- **Compliance:** Act 843 requires a lawful purpose, security safeguards, openness with data subjects, and accountability from the data controller. None of these are shown in the request.

### If Data Must Be Shared: Required Controls

- **Data minimisation:** Share activity logs only. No student profiles.  
- **Pseudonymise or anonymise:** Replace student IDs with random tokens. Remove names, emails, phone numbers, and anything that could identify a student.  
- **No production access:** Provide a separate extract in an isolated environment (e.g. a dedicated S3 bucket or analytics account), never the live database.  
- **Scoped access:** A dedicated IAM role for the partner with read-only access to that extract only, MFA required, and an expiry date.  
- **Encryption:** In transit (TLS) and at rest (KMS).  
- **Monitoring:** Logging enabled (e.g. AWS CloudTrail) and reviewed.  
- **End of project:** The partner deletes all data and confirms deletion in writing. Access is revoked.

### Documentation Required Before Any Sharing

- Signed **Data Processing / Data Sharing Agreement** with DataInsights Inc. (purpose, permitted use, security measures, no onward sharing, breach notification, deletion at end)  
- **Data Protection Impact Assessment (DPIA)** covering the cross-border transfer  
- **Vendor security assessment** of DataInsights Inc.  
- Updated **privacy notice** telling students how their data is used  
- **Access approval record** signed by the Data Owner, DPO and Information Security  
- Confirmation that EduConnect's registration with Ghana's **Data Protection Commission** covers this processing

### Action Steps

1. **Immediately rotate the exposed credentials** and remove them from the ticket. Check the access logs to confirm they were not used.  
2. Deny the request as submitted and explain why to Chris, offering the compliant route.  
3. Escalate to the DPO and Legal for review of the partnership agreement.  
4. Once documentation is approved, prepare a pseudonymised activity-log extract in an isolated environment.  
5. Create a scoped, time-limited IAM role for the partner with MFA.  
6. Turn on logging and set a review date.  
7. Record all approvals and the access expiry date in the ticket.

---

## Request 3: Archive & Deletion

**Requester:** Nancy, Customer Support Lead **Asked for:** Guidance on a deletion request from student Derrick **Status:** Account inactive, last course completed 6 months ago, no pending payments **Data classification:** All student data (CONFIDENTIAL)

### Data Access Decision Form

| Field | Response |
| :---- | :---- |
| **Decision** | **Approve** (fulfil the request), with limited retention of records the law requires us to keep |
| **Lifecycle Stage** | **Destroy** for most data. **Archive** for the small set of records that must legally be kept. |

### Can the Request Be Fulfilled?

**Yes, mostly.** Derrick has no active courses and no outstanding payments, so EduConnect has no ongoing reason to keep most of his data.

Note: Act 843 does not use the phrase "right to be forgotten" (that wording comes from the EU's GDPR). But it gives similar protection:

- **Retention:** personal data should not be kept longer than needed for the purpose it was collected for, unless a law requires it or another lawful reason applies (Section 24).  
- **Correction and deletion:** a data subject can ask the controller to delete personal data the controller is no longer authorised to keep (Section 33).

### What Can Be Retained and Why

| Data | Keep? | Reason |
| :---- | :---- | :---- |
| Profile, contact details, login data | Delete | No longer needed |
| Learning activity, progress, grades | Delete (or fully anonymise) | No longer needed for the original purpose |
| Marketing preferences, support history | Delete | No longer needed |
| Payment and invoice records | Keep (archived, restricted) | Required by tax and accounting laws for a set period. Confirm the exact period with Finance/Legal. Delete when it ends. |
| Anonymised, aggregated statistics | Keep | No longer personal data once it cannot identify Derrick |
| Minimal deletion record (ticket ID, date, what was deleted) | Keep | Proof that the request was handled. Contains no profile data. |

### Process for Customer Support

1. **Log the request** in the ticketing system with the date received.  
2. **Verify identity:** confirm the request really comes from Derrick (e.g. reply from his registered email) before deleting anything.  
3. **Notify the DPO** so the request is tracked for compliance.  
4. **Map the data:** list everywhere Derrick's data exists (production database, analytics, email/marketing tools, CRM, support tool, backups, any third-party processors).  
5. **Engineering deletes** data from live systems. Payment records are moved to restricted archive storage.  
6. **Backups:** Derrick's data is removed when backups rotate out. If a backup is restored before then, his data is deleted again.  
7. **Notify processors** (e.g. email provider) to delete his data too.  
8. **Confirm to Derrick** in writing what was deleted, what was kept and why, and how long it will be kept.  
9. **Close the ticket** with the deletion record.

### Action Steps

1. Reply to Nancy confirming the request can be fulfilled and share the process above.  
2. Nancy verifies Derrick's identity and logs the request.  
3. DPO reviews and approves the retention list.  
4. Engineering runs the deletion and archives payment records with restricted access.  
5. Set a scheduled task to delete the archived payment records when the legal retention period ends.  
6. Support sends the confirmation to Derrick.

---

## Summary

| Request | Decision | Lifecycle Stage | Key Principle |
| :---- | :---- | :---- | :---- |
| 1\. Marketing Campaign | Conditional | Use (risk of Share) | Least privilege: send the campaign without exposing CONFIDENTIAL contact data; consent required |
| 2\. Analytics Partnership | Deny (as submitted) | Share | Compliance: no agreement, cross-border transfer, exposed credentials, excessive access |
| 3\. Archive & Deletion | Approve | Destroy \+ Archive | Retention limits under Act 843: delete what's no longer needed, keep only what law requires |

