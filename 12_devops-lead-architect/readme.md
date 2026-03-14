It looks like you’ve been taking notes on **ITIL (Information Technology Infrastructure Library)** frameworks, specifically focusing on **Incident and Problem Management** workflows.

Your handwriting covers the lifecycle of a ticket, from initial reporting to Root Cause Analysis (RCA). Here is a structured breakdown of your notes.

---

## **1. Incident & Problem Management Overview**

The primary goal is to address incidents (disruptions) and prevent them from recurring (Problem Management).

* **Incident Manager Role:** Sees incidents happening repeatedly and initiates a **Problem Record**.
* **The "5 Whys" for Root Cause Analysis (RCA):**
To find the root cause of an incident, ask "Why?" five times. Example scenarios from your notes:
* Why is the server load high?
* Why is the [DB] drive full?
* Why did logs increase?
* Why was there a bulk upload?
* Why did the user make a mistake?


* **Preventative Action:** Determine what should be done so that the failure does not happen again.

---

## **2. Support Levels & Ticket Flow**

The notes outline a clear hierarchy for handling issues:

* **Tiers of Support:** L1, L2, and L3.
* **Assignment & Resolution:**
* Tickets are assigned based on the issue type.
* **L2 follows up** if the standard operating procedure (SOP) isn't enough.
* If L2 cannot resolve the issue, it is escalated (dropped/assigned) to **L3**.


* **Service Level Agreements (SLA):**
* Priority levels: **P1, P2, P3, P4.**
* Priority is based on **Business Impact** and **Urgency**.


* **Post-Resolution Workflow:**
* After a fix, the ticket is reassigned to the **BU (Business Unit)**.
* The ticket stays in "Resolved" status for **5 days** for user confirmation before moving to "Closed."



---

## **3. Software Development & Integration (SDLC)**

This section covers how changes and bugs are managed within the development environment.

* **Bug Management:** Problem Management raises bugs regarding code issues.
* **Environments:** Issues are detected and tested in **Dev** and **Staging** environments.
* **Key Roles & Stakeholders:**
* App Architects, Team Leads, and Developers (TL/Dev).
* Stakeholders involved in weekly/daily syncs.


* **Testing Phases:**
* Unit Testing.
* SIT (System Integration Testing).
* UAT (User Acceptance Testing).



---

## **4. Activity & Frameworks**

* **ITIL Framework:** Explicitly mentioned as the framework for ITSM (IT Service Management).
* **Daily Activities (BAU - Business As Usual):**
* Checking dependencies.
* Reviewing the "Final Cat" (Category) of issues.
* Targeting a resolution count (e.g., 40 resolutions in 1 day).



---

### **Summary Table: Priority & Impact**

| Priority | Impact Level | Action |
| --- | --- | --- |
| **P1 / P2** | High Business Impact | Urgent/Immediate Attention |
| **P3 / P4** | Lower Impact | Routine Management |
| **RCA** | Post-Incident | Mandatory for recurring issues to find the "Why" |

---

**Would you like me to expand on any specific part of this process, such as how to write a formal Root Cause Analysis (RCA) based on your "5 Whys" section?**

Since you’re looking at how these ITIL processes actually function in a real-world environment, it’s helpful to see how an **Incident** transitions into a **Problem** and eventually a **Change Request**.

Based on your notes, here is a more detailed breakdown of the workflow you've outlined:

---

## **1. The Lifecycle of an Incident**

An Incident is any unplanned interruption to an IT service. Your notes follow the path from the moment a user reports an issue until it is permanently fixed.

### **The Support Tiers (L1 → L2 → L3)**

* **L1 (Front Desk):** Basic troubleshooting (e.g., password resets, clearing cache).
* **L2 (Technical Support):** Follows the **SOP (Standard Operating Procedure)**. If the SOP doesn't solve it, they investigate deeper.
* **L3 (Dev/Engineering):** If the issue requires a code fix or deep infrastructure change, L2 "drops/assigns" it here.

### **Resolution vs. Closure**

Your notes specify a **5-day window**. This is a standard industry practice:

1. **Resolved:** The technician believes it's fixed.
2. **User Confirmation:** The user has 5 days to test.
3. **Closed:** After 5 days of silence or confirmation, the ticket is officially closed and cannot be reopened.

---

## **2. Moving from Incident to Problem (The "Why")**

When an Incident happens "again and again" (as noted in your top-left section), the Incident Manager creates a **Problem Record**.

### **Root Cause Analysis (RCA) - The 5 Whys**

To prevent recurrence, you perform an RCA. Using the example in your notes:

1. **Why** is the application slow? (The Server load is high).
2. **Why** is the server load high? (The Drive is full).
3. **Why** is the drive full? (Logs increased significantly).
4. **Why** did logs increase? (Because of a bulk upload).
5. **Why** did a bulk upload cause this? (Lack of automated log rotation/cleanup).

**The Solution:** Implement log rotation so the drive never fills up again.

---

## **3. The SDLC & Testing Phase**

Once Problem Management identifies a code bug, it enters the **SDLC (Software Development Life Cycle)**:

* **SIT (System Integration Testing):** Ensuring the fix doesn't break other parts of the system.
* **UAT (User Acceptance Testing):** The "Business" or "User" tests the fix in a staging environment to ensure it meets their needs.
* **Deployment:** The fix is moved from **Staging** to **Production**.

---

## **4. Prioritization Matrix**

You mentioned **Impact** and **Urgency**. These determine the **Priority (P1-P4)**.

| Urgency / Impact | High (Company-wide) | Medium (Department) | Low (Single User) |
| --- | --- | --- | --- |
| **High** | **P1 (Critical)** | P2 (High) | P3 (Medium) |
| **Medium** | P2 (High) | P3 (Medium) | P4 (Low) |

---

### **A quick tip for your studies:**

In your notes, you mentioned **"Check Dependencies."** In ITIL, this is often part of **Change Management**. Before you apply a fix (a "Change"), you must check what other systems depend on that server so you don't accidentally cause a second incident while fixing the first one.

**Would you like me to create a mock "Root Cause Analysis" (RCA) template based on these notes that you could use for practice?**


To help you practice for your coursework and professional goals, here is a structured **Root Cause Analysis (RCA)** template. This template connects your notes on the "5 Whys" with the technical environments (Dev/Staging/Prod) and support tiers you've been studying.

---

## **Root Cause Analysis (RCA) Report**

### **1. Incident Overview**

* **Problem Record ID:** PRB-2026-001
* **Associated Incident(s):** INC-9928 (Recurring Server Slowdown)
* **Priority:** P2 (High Impact)
* **Environment:** Production

### **2. Incident Description**

The application experienced significant latency over a 4-hour period. Users reported "Timeout" errors during peak business hours. Preliminary investigation by **L2 Support** showed CPU spikes and disk saturation.

---

### **3. The "5 Whys" Investigation**

* **Why 1:** Why was the application slow?
* *Answer:* The server load was excessively high, causing delayed response times.


* **Why 2:** Why was the server load high?
* *Answer:* The root partition drive was 100% full, preventing the application from writing temporary files.


* **Why 3:** Why was the drive full?
* *Answer:* System logs increased from 500MB to 50GB within a 2-hour window.


* **Why 4:** Why did logs increase so much?
* *Answer:* A bulk upload of legacy data was triggered without disabling "Debug" level logging.


* **Why 5:** Why did the bulk upload cause a crash?
* *Answer:* There was no automated log rotation policy or monitoring alert for disk space at the 80% threshold.



---

### **4. Root Cause (Final Conclusion)**

The root cause was a **lack of Infrastructure Monitoring and Log Management**. While the bulk upload was the "trigger," the system failed because it couldn't handle the resulting log volume.

### **5. Corrective & Preventive Actions (CAPA)**

| Action Item | Assigned To | Target Date |
| --- | --- | --- |
| **Immediate Fix:** Clear logs and restart services in Prod. | L2 Support | Completed |
| **Short-term:** Configure Logrotate to compress/delete logs weekly. | App Architect | 2026-03-20 |
| **Long-term:** Set up CloudWatch/Prometheus alerts for Disk Usage > 80%. | DevOps Team | 2026-03-25 |
| **Process:** Update SOP to include "Disable Debug Logs" during migrations. | Team Lead | 2026-03-25 |

---

### **6. Testing & Validation**

The fix will be deployed in **Dev**, then **Staging** for **SIT/UAT** before being pushed to Production. The **Incident Manager** will monitor the "Resolved" status for **5 days** before final closure.

---

**Would you like me to create a similar template for a "Change Request" (CR) or perhaps a "Daily BAU Checklist" based on your notes?**


Since you are moving these into GitHub, it’s best to keep the formatting clean, professional, and Markdown-friendly.

Based on the handwritten notes you shared and our discussion on the ITIL framework, here is the structured content for those specific sections:

---

## **25. ITIL 4 & PM – Incident & Problem Management**

This section defines the distinction between restoring service and finding the root cause.

* **Incident Management (Reactive):**
* **Goal:** Restore normal service operation as quickly as possible with minimal business impact.
* **Workflow:** Handled via Tiers (L1, L2, L3). Follows **SOPs** (Standard Operating Procedures).
* **Resolution Window:** Tickets stay in "Resolved" for **5 days** for user validation before moving to "Closed."


* **Problem Management (Proactive/Diagnostic):**
* **Goal:** Identify the root cause of recurring incidents and prevent them from happening again.
* **Trigger:** When an incident happens "again and again," the Incident Manager creates a **Problem Record**.
* **Key Technique:** **The 5 Whys (RCA).** * *Example:* High Server Load $\rightarrow$ Disk Full $\rightarrow$ Log Increase $\rightarrow$ Bulk Upload $\rightarrow$ Lack of Log Rotation.



---

## **26. ITIL 4 & PM – Costing and Resourcing**

In a Project Management (PM) context, ITIL processes require careful resource allocation to remain cost-effective.

* **Resourcing (The People):**
* **L1/L2:** High volume, lower cost per ticket. Focused on BAU (Business As Usual) and daily resolutions (e.g., target of 40 resolutions/day).
* **L3/Architects:** High cost, specialized skill sets. Involved in **SIT** (System Integration Testing) and **UAT** (User Acceptance Testing).


* **Costing Factors:**
* **SLA Penalties:** Financial cost incurred if P1/P2 incidents are not resolved within the agreed timeframe.
* **Resource Utilization:** Balancing "Run" (Support) vs. "Build" (New Projects). If too many resources are stuck in Incident Management, project development (SDLC) costs rise due to delays.


* **Environment Costs:** Maintaining **Dev**, **Staging**, and **Production** environments for testing bug fixes before deployment.

---

## **28. Major Incident in Production**

A Major Incident (MI) is a "P1" or "Critical" event that requires an immediate, coordinated response.

* **Characteristics:**
* High Business Impact (e.g., entire site down, data breach, or payment gateway failure).
* Requires a **Major Incident Team** (Managers, Architects, and Leads).


* **The Command Flow:**
1. **Detection:** Usually via automated monitoring or high-volume L1 reports.
2. **War Room:** Stakeholders (App Architects, TLs) join a live sync to troubleshoot.
3. **Communication:** Regular updates to the Business Unit (BU) regarding the "Impact" and "Estimated Time of Resolution" (ETR).
4. **Post-Mortem:** After restoration, an mandatory **RCA** is conducted to ensure the "Why" is addressed so it never happens again.



---

**Would you like me to format these specifically into a `README.md` file for your GitHub repository?**





