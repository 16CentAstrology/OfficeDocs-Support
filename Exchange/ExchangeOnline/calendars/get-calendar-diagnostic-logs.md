---
title: Get calendar diagnostic logs for Exchange Online mailboxes
description: Discusses how to collect CDL logs to investigate calendar issues for Exchange Online mailboxes.
author: cloud-writer
ms.author: meerak
manager: dcscontentpm
audience: ITPro
ms.topic: troubleshooting
ms.custom: 
  - sap:Calendaring
  - Exchange Online
  - CSSTroubleshoot
  - CI 187189
ms.reviewer: meshel, shanefe, erichart, ninob, meerak, v-trisshores
appliesto:
  - Exchange Online
  - Outlook on the web
  - Outlook for Windows
  - Outlook for Mac
  - Outlook for iOS
  - Outlook for Android
  - Outlook for Microsoft 365
  - Outlook 2021
  - Outlook 2019
  - Outlook 2016
  - New Outlook for Windows
search.appverid: MET150
ms.date: 08/27/2024
---

# Get calendar diagnostic logs for Exchange Online mailboxes

Calendar diagnostic logs (CDLs) contain important calendar-related event data for Microsoft Exchange Online mailboxes. You can use CDLs to get detailed information about calendar items, such as meetings, to help diagnose issues.

The following sections present different ways to get the CDLs for a meeting.

To diagnose a meeting issue, we recommend that you [use the Get-CalendarDiagnosticObjectsSummary.ps1 script](#use-the-get-calendardiagnosticobjectssummaryps1-script) because the script processes the raw CDLs to provide enhanced CDLs and a concise timeline of meeting actions. For information about how to analyze the data, see [Analyze calendar diagnostic logs for Exchange Online mailboxes](./analyze-calendar-diagnostic-logs.md).

## Use the Get-CalendarDiagnosticObjectsSummary.ps1 script

Follow these steps to get raw CDLs, enhanced CDLs, and a concise timeline of meeting actions:

1. Download the [Get-CalendarDiagnosticObjectsSummary.ps1](https://github.com/microsoft/CSS-Exchange/releases/latest/download/Get-CalendarDiagnosticObjectsSummary.ps1) script to a local folder.

2. If the [ImportExcel module](https://www.powershellgallery.com/packages/ImportExcel) isn't already installed, run the following PowerShell cmdlet to install it:

   ```PowerShell
   Install-Module -Name ImportExcel
   ```

3. [Connect to Exchange Online PowerShell](/powershell/exchange/connect-to-exchange-online-powershell).

4. In the folder that contains the script, run the following PowerShell command:

   ```PowerShell
   .\Get-CalendarDiagnosticObjectsSummary.ps1 -Identity "<mailbox SMTP address>" -Subject "<meeting subject>" -Exceptions -ExportToExcel
   ```

   The script creates the following Microsoft Excel file in the current folder:

   *CalLogSummary_\<short meeting ID\>.xlsx*

   The file contains the following set of worksheets:

   - `<SMTP address>_TimeLine`: Contains the timeline.
   - `<SMTP address>`: Contains the enhanced CDLs.
   - `<SMTP address>_Raw`: Contains the raw CDLs.

   > [!NOTE]
   > - If more than one meeting matches the meeting subject, the script generates an Excel file for each meeting.
   > - Each meeting is uniquely identified by a meeting ID (GUID). You can find the meeting ID in the timeline worksheet for the meeting.

5. Gather the following information:

   - The SMTP address of each key participant, such as the meeting organizer, delegates, and attendees.
   - The meeting ID, such as `040000008200E00074C5B7101A82E00800000000A0D1E89273EFDA010000000000000000100000002D9427567554FA4AA9162A58A8B968CB`.

6. In the folder that contains the script, run the following PowerShell command, and pass in the information from step 5:

   ```PowerShell
   .\Get-CalendarDiagnosticObjectsSummary.ps1 -Identity "<organizer SMTP address>","<delegate SMTP address>","<attendee SMTP address>" -MeetingId "<meeting ID>" -Exceptions -ExportToExcel
   ```

   > [!NOTE]
   > - Use the **Identity** parameter to specify all key participants of the meeting.
   > - You must use the **MeetingId** parameter instead of the **Subject** parameter if you specify multiple participants.

   The script creates the following Excel file in the current folder:

   *CalLogSummary_\<short meeting ID\>.xlsx*

   The file contains the following set of worksheets for each meeting participant:

   - `<participant SMTP address>_TimeLine`: Contains the timeline.
   - `<participant SMTP address>`: Contains the enhanced CDLs.
   - `<participant SMTP address>_Raw`: Contains the raw CDLs.

## Use the Get-CalendarDiagnosticObjects cmdlet

Run the [Get-CalendarDiagnosticObjects](/powershell/module/exchange/get-calendardiagnosticobjects) PowerShell cmdlet to download raw CDLs.

> [!NOTE]
> - The Get-CalendarDiagnosticObjects cmdlet doesn't generate enhanced CDLs or a meeting timeline.
> - If multiple meetings have the same subject, use the meeting ID to uniquely specify a meeting. The meeting ID is in the `CleanGlobalObjectID` column of the output file that's generated by the cmdlet.

## Use the EAC

Follow these steps to download raw CDLs:

1. In the EAC, navigate to **Troubleshoot** > **Collect Logs** > **Calendar**.

2. Select **Calendar Logs** to open the **Calendar Diagnostic Logs** pane.

3. Enter the following information:

   - SMTP address of the calendar owner
   - Meeting subject

4. Select **Start**. 

Exchange Online downloads the raw CDLs to your browser's download folder.

> [!NOTE]
> - The EAC **Troubleshoot** menu is being rolled out gradually and might not yet be available in your organization.
> - The EAC doesn't generate enhanced CDLs or a meeting timeline.