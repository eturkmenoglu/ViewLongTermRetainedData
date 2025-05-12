# 📂 Long-Term Retained Emails Viewer for Power Apps (Case Form Extension)
> ⚠️ **DISCLAIMER:**  
> **This is a sample app intended to demonstrate Microsoft Power Platform capabilities. It is not designed for production use. The implementation may contain potential unseen issues or errors. Please test thoroughly and adapt as needed before considering it for any production environment.**

---
## Overview

This project is a **solution accelerator** designed to enhance the user experience for enterprise business users and audit teams who need to access **archived emails and their attachments** within a **Dataverse Case form** using **Power Apps**.

While Microsoft’s **Long Term Retention (LTR) Policies** reduce Dataverse storage by archiving old activities (emails, phone calls, etc.) into Azure Blob Storage, they limit the accessibility and usability of that data—especially for audit or compliance scenarios. This solution bridges that gap by surfacing archived emails and attachments directly inside Power Apps in a user-friendly way.

---

## 🎯 Problem Statement

In enterprise environments:

- The `activitypointer` table often grows excessively large due to retained communication history, especially `email` records.
- Microsoft’s **LTR** offloads these records from active storage, reducing storage costs.
- However, LTR only supports **read-only access via the main grid**, making it challenging to explore emails and their attachments contextually (e.g., within a case).

---

## ✅ Solution Summary

This accelerator introduces a **Canvas Power App** embedded in the Case form that:

- Fetches archived (retained) email data related to a specific Case.
- Displays the email subject, body, sender details, and attachment metadata.
- Allows users to download **email attachments**, including base64-encoded inline images.

---

## 📌 How the Solution Works End-to-End

Here’s a step-by-step breakdown of how everything fits together:

1. **LTR Archival**  
   Emails older than a configured retention period are removed from the live `email` table and moved to Azure Blob Storage by Microsoft's Long Term Retention policy.

2. **Fetch Archived Data**  
   Power Automate flows are used to query archived records using the special `datasource="retained"` flag (in FetchXML), allowing retrieval of emails tied to a specific Case ID.

3. **Display in Power App**  
   The data is presented in a Canvas App embedded inside the Case form, enabling contextual and user-friendly access to archived emails.

4. **Download Attachments**  
   Attachments and inline images are retrieved and made downloadable using the [Canvas File Downloader PCF control](https://pcf.gallery/canvas-file-downloader/), even when stored in base64.

5. **No Rehydration Needed**  
   Users can access necessary records and files without re-importing them into active storage—preserving Dataverse capacity and compliance.

---

## 🔍 Key Features

- Embedded directly inside the Case form for context-aware auditing.
- Secure access to long-term retained emails and their attachments.
- No need to restore or rehydrate archived emails into Dataverse.
- Fully customizable for subject-based filtering, timestamp sorting, or sender scoping.

---

## 🚀 Future Enhancements

- Role-based access controls for sensitive attachment visibility.
- Audit logging of who viewed/downloaded archived content.
- Expand support for other activity types (e.g., phone calls, appointments).

---

## 📁 Dependencies

- Microsoft Dataverse with Long Term Retention enabled.
- Power Apps (Canvas App embedded in Model-Driven App).
- Power Automate (Cloud Flows).
- PCF Gallery – Canvas File Downloader control.

---

## 🧠 Author Notes

This solution is built to empower audit and support teams without compromising Dataverse storage optimization. Feel free to extend or adapt this accelerator based on your specific retention policy structure or compliance needs.

---

## 📝 License

MIT License or as applicable to your internal deployment model.

---

## 🖼️ Example Output

Here is how the outcome looks like inside the Case form:

![ViewLongTermRetainedData](images/LTR_Viewer.png)

> _This screenshot shows retained email records with subject, sender info, and a download link for attachments inside a Canvas App._

---


#### Sample FetchXML:

```xml
<fetch version="1.0" output-format="xml-platform" mapping="logical" distinct="true" no-lock="false" datasource="retained">
  <entity name="email">
    <attribute name="statecode"/>
    <attribute name="subject"/>
    <attribute name="description"/>
    <attribute name="regardingobjectid"/>
    <attribute name="activityid"/>
    <attribute name="prioritycode"/>
    <attribute name="actualend"/>
    <attribute name="from"/>
    <order attribute="actualend" descending="true"/>
    <filter type="and">
      <condition attribute="subject" operator="not-null"/>
      <condition attribute="regardingobjectid" operator="eq" value="&lt;YOUR CASEID&gt;" uitype="incident"/>
    </filter>
  </entity>
</fetch>

