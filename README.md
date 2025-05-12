# 📂 Long-Term Retained Emails Viewer for Power Apps (Case Form Extension)

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

## ⚙️ How It Works

### 1. Power Automate Integration

A Power Automate flow executes a `FetchXML` query with the special `datasource="retained"` flag to retrieve archived email records.

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
