---
title: ASR rules deployment phase 3 - implement
description: Provides guidance to implement your attack surface reduction rules deployment.
keywords: Attack surface reduction rules deployment, ASR deployment, enable asr rules, configure ASR, host intrusion prevention system, protection rules, anti-exploit rules, anti-exploit, exploit rules, infection prevention rules, Microsoft Defender for Endpoint, configure ASR rules
search.product: eADQiWindows 10XVcnh
ms.reviewer:
ms.prod: m365-security
ms.mktglfcycl: manage
ms.sitesec: library
ms.pagetype: security
ms.localizationpriority: medium
audience: ITPro
author: jweston-1
ms.author: v-jweston
manager: dansimp
ms.custom: asr
ms.technology: mde
ms.topic: article
ms.collection: M365-security-compliance
---

# Attack surface reduction rules deployment phase 3: implement

The implementation phase moves the ring from testing into functional state.

> [!div class="mx-imgBorder"]
> ![ASR rules implementation steps](images/asr-rules-implementation-steps.png)

## Step 1: Transition ASR Rules from Audit to Block

1. After all exclusions are determined while in audit mode, start setting some ASR rules to "block" mode, starting with the rule that has the fewest triggered events. See” [Enable attack surface reduction rules](enable-attack-surface-reduction.md).
2. Review the reporting page in the Microsoft 365 Defender portal; see [Threat protection report in Microsoft Defender for Endpoint](threat-protection-reports.md). Also review feedback from your ASR champions.
3. Refine exclusions or create new exclusions as determined necessary.
4. Switch problematic rules back to Audit.

  >[!Note]
  >For problematic rules (rules creating too much noise), it is better to create exclusions than to turn rules off or switching back to Audit. You will have to determine what is best for your environment.

  >[!Tip]
  >When available, take advantage of the Warn mode setting in rules to limit disruptions. Enabling ASR rules in Warn mode enables you to capture triggered events and view their potential disruptions, without actually blocking end-user access. Learn more: [Warn mode for users](attack-surface-reduction.md#warn-mode-for-users).

### How does Warn mode work?

Warn mode is effectively a Block instruction, but with the option for the user to “Unblock” subsequent executions of the given flow or app. Warn mode unblocks on a per device, user, file and process combination. The warn mode information is stored locally and has a duration of 24 hours.

### Step 2: Expand deployment to ring n + 1

When you are confident that you have correctly configured the ASR rules for ring 1, you can widen the scope of your deployment to the next ring (ring n + 1).

The deployment process, steps 1 – 3,  is essentially the same for each subsequent ring:

1. Test rules in Audit
2. Review ASR-triggered audit events in the Microsoft 365 Defender portal
3. Create exclusions
4. Review: refine, add, or remove exclusions as necessary
5. Set rules to “block”
6. Review the reporting page in the Microsoft 365 Defender portal.
7. Create exclusions.
8. Disable problematic rules or switch them back to Audit.

#### Customize attack surface reduction rules

As you continue to expand your attack surface reduction rules deployment, you may find it necessary or beneficial to customize the attack surface reduction rules that you have enabled.

##### Exclude files and folders

You can choose to exclude files and folders from being evaluated by attack surface reduction rules. When excluded, the file won't be blocked from running even if an attack surface reduction rule detects that the file contains malicious behavior.

For example, consider the ransomware rule:

The ransomware rule is designed to help enterprise customers reduce risks of ransomware attacks while ensuring business continuity. By default, the ransomware rule errors on the side of caution and protect against files that haven't yet attained sufficient reputation and trust. To reemphasize, the ransomware rule only triggers on files that have not gained enough positive reputation and prevalence, based on usage metrics of millions of our customers. Usually, the blocks are self resolved, because each file's "reputation and trust" values are incrementally upgraded as non-problematic usage increases.

In cases in which blocks aren't self resolved in a timely manner, customers can - _at their own risk_ - make use of either the self-service mechanism or an Indicator of Compromise (IOC)-based "allow list" capability to unblock the files themselves.

> [!WARNING]
> Excluding or unblocking files or folders could potentially allow unsafe files to run and infect your devices. Excluding files or folders can severely reduce the protection provided by attack surface reduction rules. Files that would have been blocked by a rule will be allowed to run, and there will be no report or event recorded.

An exclusion applies to all rules that allow exclusions. You can specify an individual file, folder path, or the fully qualified domain name for a resource. However, you cannot limit an exclusion to a specific rule.

An exclusion is applied only when the excluded application or service starts. For example, if you add an exclusion for an update service that is already running, the update service will continue to trigger events until the service is stopped and restarted.

Attack surface reduction supports environment variables and wildcards. For information about using wildcards, see [use wildcards in the file name and folder path or extension exclusion lists](configure-extension-file-exclusions-microsoft-defender-antivirus.md#use-wildcards-in-the-file-name-and-folder-path-or-extension-exclusion-lists).
If you are encountering problems with rules detecting files that you believe should not be detected, [use audit mode to test the rule](evaluate-attack-surface-reduction.md).

See the [attack surface reduction rules reference](attack-surface-reduction-rules-reference.md) topic for details on each rule.

##### Use Group Policy to exclude files and folders

1. On your Group Policy management computer, open the [Group Policy Management Console](https://technet.microsoft.com/library/cc731212.aspx), right-click the Group Policy Object you want to configure and select **Edit**.

2. In the **Group Policy Management Editor**, go to **Computer configuration** and click **Administrative templates**.

3. Expand the tree to **Windows components** \> **Microsoft Defender Antivirus** \> **Microsoft Defender Exploit Guard** \> **Attack surface reduction**.

4. Double-click the **Exclude files and paths from Attack surface reduction Rules** setting and set the option to **Enabled**. Select **Show** and enter each file or folder in the **Value name** column. Enter **0** in the **Value** column for each item.

> [!WARNING]
> Do not use quotes as they are not supported for either the **Value name** column or the **Value** column.

##### Use PowerShell to exclude files and folders

1. Type **powershell** in the Start menu, right-click **Windows PowerShell** and select **Run as administrator**.

2. Enter the following cmdlet:

    ```PowerShell
    Add-MpPreference -AttackSurfaceReductionOnlyExclusions "<fully qualified path or resource>"
    ```

    Continue to use `Add-MpPreference -AttackSurfaceReductionOnlyExclusions` to add more folders to the list.

    > [!IMPORTANT]
    > Use `Add-MpPreference` to append or add apps to the list. Using the `Set-MpPreference` cmdlet will overwrite the existing list.

##### Use MDM CSPs to exclude files and folders

Use the [./Vendor/MSFT/Policy/Config/Defender/AttackSurfaceReductionOnlyExclusions](/windows/client-management/mdm/policy-csp-defender#defender-attacksurfacereductiononlyexclusions) configuration service provider (CSP) to add exclusions.

##### Customize the notification

You can customize the notification for when a rule is triggered and blocks an app or file. See the [Windows Security](/windows/security/threat-protection/windows-defender-security-center/windows-defender-security-center#customize-notifications-from-the-windows-defender-security-center) article.

## Additional topics in this deployment collection

[ASR rules deployment guide - overview](attack-surface-reduction-rules-deployment.md)

[ASR rules deployment phase 1 - plan](attack-surface-reduction-rules-deployment-phase-1.md)

[ASR rules deployment phase 2 - test](attack-surface-reduction-rules-deployment-phase-2.md)

[ASR rules deployment phase 4 - operationalize](attack-surface-reduction-rules-deployment-phase-4.md)