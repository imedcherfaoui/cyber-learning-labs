# Master Playbook: SOC L1 Alert Management & Triage Workflow

## 1. From Raw Events to SIEM Alerts

In an enterprise environment, thousands of systems (OS, firewalls, cloud infrastructure) generate millions of individual events (user logins, process executions, file downloads) daily. Reviewing these manually is impossible.

- **The Pipeline:**
  `Raw System Event` $\rightarrow$ `Shipped via Agent/Syslog` $\rightarrow$ `Centralized SIEM/EDR` $\rightarrow$ `Detection Rule Match` $\rightarrow$ `Generated Alert`.
- **The Analyst Value:** Alerts save analysts from raw log review by highlighting anomalous or suspicious activities, reducing a mountain of millions of logs down to a manageable queue of daily alerts.

### Alert Management Platforms

| Solution Type | Examples                                          | Purpose in the SOC                                                                                    |
| :------------ | :------------------------------------------------ | :---------------------------------------------------------------------------------------------------- |
| **SIEM**      | Splunk Enterprise Security, Elastic SIEM | Solid alert management and core log aggregation; the standard choice for most SOCs.          |
| **EDR / NDR** | Microsoft Defender, CrowdStrike          | Endpoint/Network telemetry and localized alert dashboards; usually fed upstream into a SIEM. |
| **SOAR**      | Splunk SOAR, Cortex SOAR                 | Used by larger teams to automate response actions and centralize alerts from multiple tools. |
| **ITSM**      | Jira, TheHive, Trello                    | Custom ticket management systems used to track investigations and incident response tasks.   |

---

## 2. SOC Team Roles & Responsibilities

- **SOC L1 Analysts:** The first line of defense. Review the alert queue, execute triage workflows, distinguish benign noise from real attacks, and escalate true threats to L2.
- **SOC L2 Analysts:** Handle escalated incidents. Perform deep-dive forensic investigations, coordinate containment, and drive remediation.
- **SOC Engineers:** Build and tune detection rules, ensuring alerts contain sufficient contextual fields for efficient triage.
- **SOC Manager:** Monitor metrics (MTTD - Mean Time to Detect, MTTR - Mean Time to Respond), track triage quality, and oversee team operations.

---

## 3. Anatomy of a Security Alert

Before touching an alert, an analyst must understand its key metadata properties:

1.  **Alert Time vs. Event Time:** Alert time is when the detection rule triggered (usually a few minutes after the event); Event time is when the actual action occurred on the endpoint.
2.  **Alert Name:** A summary description derived from the detection rule (e.g., _Unusual Login Location_, _Windows RDP Brute Force_, _Unapproved Mimikatz Usage_).
3.  **Alert Severity:** Initial urgency level set by detection engineers ([🟢 Low/Info], [🟡 Medium], [🟠 High], [🔴 Critical]). Analysts can adjust this if context warrants it.
4.  **Alert Status:** Tracks lifecycle workflow ([🆕 New/Unassigned], [🔄 In Progress], [✅ Closed/Resolved]).
5.  **Alert Verdict (Classification):** Final determination of the alert nature ([🔴 True Positive / Real Threat], [🟢 False Positive / Noise]).
6.  **Alert Assignee:** The specific analyst who has taken ownership of the alert and is responsible for its resolution.
7.  **Alert Description:** Detailed text explaining the rule logic, why the activity is malicious, and recommended triage steps.
8.  **Alert Fields / Context:** Specific technical parameters attached to the alert (Affected Hostname, User Account, Source IP, Entered Command Line).

---

## 4. Alert Prioritization Logic

With hundreds of alerts in a queue, picking the right alert first is vital to prevent missing active intrusions. Automated sorting logic typically follows this hierarchy:

1.  **Filter the Queue:** Exclude alerts already assigned or reviewed by teammates.
2.  **Sort by Severity (Descending):** Start with 🔴 _Critical_ and 🟠 _High_ alerts, as they carry higher fidelity and massive impact potential, then move to _Medium_ and _Low_.
3.  **Sort by Time (Ascending):** Start with the **oldest** unresolved alerts. _Rationale:_ An attacker from an older breach is likely already in the data-exfiltration phase, whereas a newer alert has just initiated the reconnaissance phase.

---

## 5. Standard Operating Procedure: The Alert Triage Lifecycle

The operational flowchart below maps out the exact step-by-step path an L1 analyst must follow during an investigation:

![SOC Alert Triage Workflow](../assets/image_2c11a8.png)

### Phase A: Initial Actions

- **Step 1:** Prioritize the queue and select the highest-priority alert.
- **Step 2:** Assign the alert to yourself to take ownership.
- **Step 3:** Update the alert status to **"In Progress"** to prevent duplicate work by peers.
- **Step 4:** Read the alert name, description, and extract key indicators (Hostnames, IPs, Usernames).

### Phase B: Investigation

- **Step 5:** Check if an institutional **Workbook (Playbook/Runbook)** exists for this specific alert category.
  - _If Yes:_ Follow the structured workbook steps precisely.
  - _If No:_ Proceed with manual SIEM/EDR investigation.
- **Step 6:** Identify the scope of asset threat (affected user, workstation, cloud tenant, network segment).
- **Step 7:** Examine surrounding telemetry—look at actions executed 15 minutes before and after the alert trigger.
- **Step 8:** Cross-reference indicators with Threat Intelligence platforms (VirusTotal, AlienVault OTX, AbuseIPDB).

### Phase C: Verdict & Closure

- **Step 9:** Formulate the final verdict. Is it malicious (**True Positive**) or benign (**False Positive**)?
- **Step 10:** Determine escalation requirements.
  - _If True Positive requiring L2 intervention:_ Escalate the alert to an L2 analyst with your gathered context.
  - _If False Positive / Handled:_ Proceed to closure.
- **Step 11:** Document your findings by adding a comprehensive analytical comment detailing your steps and verdict reasoning.
- **Step 12:** Return to the dashboard and move the alert status to **"Closed"**.

---

## 6. Incident Reporting & Escalation Standard

Once an alert is investigated, an analyst must transition from triage to documentation and communication. Proper reporting ensures continuity, saves L2 analysts from starting investigations from scratch, and builds long-term institutional knowledge.

![SOC Alert Triage Workflow](../assets/Alert_Funnel.svg)

### The Purpose of Alert Reporting
*   **Context for Escalation:** A well-written report provides instant context for L2 analysts handling True Positives.
*   **Permanent Records:** While raw SIEM logs are purged or archived after 3–12 months due to storage limits, ticket alerts and reports are kept indefinitely.
*   **Skill Improvement:** *“If you can't explain it simply, you don't understand it well enough.”* Writing forces clarity and solidifies analytical skills.

### The Five Ws Reporting Format
Every investigation report on a True Positive or complex alert must answer the **Five Ws**:
1.  **Who:** Which user account logged in, executed a command, or downloaded the file?
2.  **What:** What exact action or sequence of events was performed?
3.  **When:** Exact timestamps of when the suspicious activity started and ended.
4.  **Where:** Which device hostname, IP address, or external website was involved?
5.  **Why:** The core reasoning behind your final verdict (True Positive vs. False Positive).

![SOC Alert Triage Workflow](../assets/Report_Format.svg)

---

## 7. Escalation Matrix & L2 Hand-off

When an alert requires senior intervention, L1 analysts must follow established escalation pathways.

### Criteria for Escalation to L2
An alert must be escalated if:
1.  It is an indicator of a major cyberattack requiring deep Digital Forensics and Incident Response (DFIR).
2.  Active containment or remediation actions are required (e.g., host isolation, forced password reset, malware removal).
3.  Cross-departmental or external communication is mandatory (customers, legal, management, law enforcement).
4.  **Uncertainty/Stuck:** The L1 analyst does not fully understand the alert and requires senior guidance. *(Note: In your first months, always ask for help rather than blindly closing an alert you don't understand).*

### Escalation Protocol
1.  Reassign the alert ticket object to the active L2 analyst on shift.
2.  Attach your completed Five Ws report.
3.  Ping the L2 analyst via corporate channels (Teams, Slack, or in-person) with the direct ticket link and a brief summary.

---

## 8. SOC Crisis Communication Guidelines
When unexpected operational blockers occur during a shift, follow these standard resolutions:
*   **Unresponsive L2 during a Critical Alert:** Check emergency escalation lists. Try calling L2, escalate to L3, and finally notify the SOC Manager.
*   **Compromised Communication Channel:** If investigating a potential Slack/Teams account takeover, **do not** message the user through the compromised chat app. Use alternative channels (phone call, in-person).
*   **Alert Floods / Queue Overload:** Prioritize critically according to standard workflow logic, and immediately notify the shift L2 of the backlog volume.
*   **Post-Closure Regret (Misclassification):** If you realize a closed alert was actually a True Positive, **immediately** notify L2. Attackers often dwell silently; early notification can mitigate further impact.
*   **SIEM Log Parsing Failures:** Do not skip the alert. Investigate what data is available and escalate the broken parser/unsearchable log issue immediately to SOC engineers or L2.

---

## 9. Alert Enrichment: Identity, Asset & Network Context
Raw alert fields rarely tell the full story on their own. Before making a verdict, an L1 analyst enriches indicators by checking internal inventories and network architecture to determine whether behavior aligns with normal business operations.

### Identity Inventory (Identity Lookup)
An **identity inventory** catalogs employee user accounts, service/machine accounts, roles, locations, and access privileges.
*   **Why it matters:** If a financial file is accessed or shared, checking roles and locations helps verify whether the users involved have a legitimate business reason to touch that data.
*   **Common sources:**
    *   **Active Directory / Entra ID:** Core identity database frequently queried during SOC investigations.
    *   **SSO Providers (Okta, Google Workspace):** Cloud identity platforms used to look up user accounts and access.
    *   **HR Systems (BambooHR, SAP, HiBob):** Rich employee context (restricted to employees rather than service accounts).
    *   **Custom Solutions (CSV/Excel sheets):** Spreadsheets maintained by IT or security teams.

### Asset Inventory (Asset Lookup)
An **asset inventory** lists computing resources in the environment—such as servers and workstations—along with details like IP address, OS, owner, location, and purpose.
*   **Why it matters:** Knowing whether a host is a domain controller, a finance file server, a stationary office PC, or a remote laptop tells you how critical it is and what traffic is normal for it.
*   **Common sources:**
    *   **Active Directory / Entra ID:** Tracks both identities and joined hosts.
    *   **SIEM or EDR (Elastic, CrowdStrike):** Agents collect host telemetry and system metadata.
    *   **MDM Solutions (MS Intune, Jamf MDM):** Manage and inventory endpoints.
    *   **Custom Solutions (CSV/Excel sheets):** Manual or semi-manual asset lists.

### Network Diagrams & Reconstructing Attack Paths
A **network diagram** maps locations, subnets, exposed services, and traffic flows. Combining firewall logs with a network diagram lets you trace how an intrusion progresses across segments:

![Corporate Network & Subnet Topology](../assets/Network_Diagrams.png)

*   **Example Walkthrough:** An external IP (`103.61.240.174`) hits exposed VPN port `TCP/10443`, gets NAT'd to an internal VPN subnet IP (`10.10.0.53`), and then scans `172.16.15.0/24` (Database Subnet) before pivoting to `172.16.23.0/24` (Office Subnet). Mapping those subnets immediately shows an external-to-VPN foothold followed by internal lateral scanning.

---

## 10. SOC Workbooks (Playbooks / Runbooks)
A **SOC workbook** (also called a playbook, runbook, or workflow) is a structured step-by-step guide for investigating and responding to a specific alert type consistently. Because L1 analysts are early in their career and won't know every edge case instinctively, workbooks prevent skipped checks and rushed verdicts.

### Structured Triage Stages
Well-designed workbooks typically break investigation logic into sequential stages so conclusions rest on solid evidence:
1.  **Enrichment:** Gather context on the user, host, or IP using identity inventories, threat intelligence, and anonymization/reputation lookups[cite: 2, 3].
2.  **Investigation:** Pivot into SIEM/EDR logs to check surrounding activity, historical baselines (such as login timelines or typical hours), and high-risk follow-on actions (like MFA changes)[cite: 2, 3].
3.  **Escalation:** Reach out to the user when safe/appropriate, document findings, and escalate to L2 with a full report when activity is suspicious or confirmed malicious[cite: 2, 3].

### Example: Unusual Login Location Workbook

![Unusual Login Location Workbook Flow](../assets/Workbook_Example .svg)

When triaging an unusual login alert following a flow like the one above:
*   Check expected user location and context (e.g., via HR/identity sources) and look up the source IP in threat intelligence and anonymization services.
*   If the IP is confirmed malicious, move straight to escalation.
*   Otherwise, inspect follow-on actions in the SIEM (such as MFA resets), review historical login baselines (e.g., a 90-day login timeline), and evaluate whether VPN/location or login timing is atypical before closing as a False Positive or escalating to L2.