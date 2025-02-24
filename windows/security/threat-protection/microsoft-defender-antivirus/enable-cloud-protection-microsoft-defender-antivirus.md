---
title: Enable cloud-delivered protection in Microsoft Defender Antivirus
description: Enable cloud-delivered protection to benefit from fast and advanced protection features.
keywords: Microsoft Defender Antivirus, antimalware, security, cloud, block at first sight
search.product: eADQiWindows 10XVcnh
ms.pagetype: security
ms.prod: w10
ms.mktglfcycl: manage
ms.sitesec: library
ms.pagetype: security
ms.localizationpriority: medium
author: denisebmsft
ms.author: deniseb
ms.reviewer: 
manager: dansimp
ms.custom: nextgen
---

# Enable cloud-delivered protection

**Applies to:**

- [Microsoft Defender Advanced Threat Protection (Microsoft Defender ATP)](https://go.microsoft.com/fwlink/p/?linkid=2069559)

> [!NOTE]
> The Microsoft Defender Antivirus cloud service is a mechanism for delivering updated protection to your network and endpoints. Although it is called a cloud service, it is not simply protection for files stored in the cloud; rather, it uses distributed resources and machine learning to deliver protection to your endpoints at a rate that is far faster than traditional Security intelligence updates.

Microsoft Defender Antivirus uses multiple detection and prevention technologies to deliver accurate, real-time, and intelligent protection. [Get to know the advanced technologies at the core of Microsoft Defender ATP next generation protection](https://www.microsoft.com/security/blog/2019/06/24/inside-out-get-to-know-the-advanced-technologies-at-the-core-of-microsoft-defender-atp-next-generation-protection/).
![List of Windows Defender AV engines](images/microsoft-defender-atp-next-generation-protection-engines.png)  

You can enable or disable Microsoft Defender Antivirus cloud-delivered protection with Microsoft Intune, Microsoft Endpoint Configuration Manager, Group Policy, PowerShell cmdlets, or on individual clients in the Windows Security app.

See [Use Microsoft cloud-delivered protection](utilize-microsoft-cloud-protection-microsoft-defender-antivirus.md) for an overview of Microsoft Defender Antivirus cloud-delivered protection.

There are specific network-connectivity requirements to ensure your endpoints can connect to the cloud-delivered protection service. See [Configure and validate network connections](configure-network-connections-microsoft-defender-antivirus.md) for more details.

> [!NOTE]
> In Windows 10, there is no difference between the **Basic** and **Advanced** reporting options described in this topic. This is a legacy distinction and choosing either setting will result in the same level of cloud-delivered protection. There is no difference in the type or amount of information that is shared. See the [Microsoft Privacy Statement](https://go.microsoft.com/fwlink/?linkid=521839) for more information on what we collect.

## Use Intune to enable cloud-delivered protection

1. Sign in to the [Azure portal](https://portal.azure.com).
2. Select **All services > Intune**.
3. In the **Intune** pane, select **Device configuration > Profiles**, and then select the **Device restrictions** profile type you want to configure. If you haven't yet created a **Device restrictions** profile type, or if you want to create a new one, see [Configure device restriction settings in Microsoft Intune](https://docs.microsoft.com/intune/device-restrictions-configure).
4. Select **Properties**, select **Settings: Configure**, and then select **Microsoft Defender Antivirus**.
5. On the **Cloud-delivered protection** switch, select **Enable**.
6. In the **Prompt users before sample submission** dropdown, select **Send all data without prompting**.
7. In the **Submit samples consent** dropdown, select one of the following:

    - **Send safe samples automatically**
    - **Send all samples automatically**

        >[!NOTE]
        > The **Send safe samples automatically** option means that most samples will be sent automatically. Files that are likely to contain personal information will still prompt and require additional confirmation.

        > [!WARNING]
        > Setting to **Always Prompt** will lower the protection state of the device. Setting to **Never send** means the [Block at First Sight](configure-block-at-first-sight-microsoft-defender-antivirus.md) feature of Microsoft Defender ATP won't work.

8. Click **OK** to exit the **Microsoft Defender Antivirus** settings pane, click **OK** to exit the **Device restrictions** pane, and then click **Save** to save the changes to your **Device restrictions** profile.

For more information about Intune device profiles, including how to create and configure their settings, see [What are Microsoft Intune device profiles?](https://docs.microsoft.com/intune/device-profiles)

## Use Configuration Manager to enable cloud-delivered protection

See [How to create and deploy antimalware policies: Cloud-protection service](https://docs.microsoft.com/configmgr/protect/deploy-use/endpoint-antimalware-policies#cloud-protection-service) for details on configuring Microsoft Endpoint Configuration Manager (current branch).

## Use Group Policy to enable cloud-delivered protection

1. On your Group Policy management computer, open the [Group Policy Management Console](https://technet.microsoft.com/library/cc731212.aspx), right-click the Group Policy Object you want to configure and click **Edit**.

2. In the **Group Policy Management Editor** go to **Computer configuration**.

3. Select **Administrative templates**.

4. Expand the tree to **Windows components > Microsoft Defender Antivirus > MAPS**

5. Double-click **Join Microsoft MAPS**. Ensure the option is enabled and set to **Basic MAPS** or **Advanced MAPS**. Select **OK**.

6. Double-click **Send file samples when further analysis is required**. Ensure that the option is set to **Enabled** and that the other options are either of the following:

    1. **Send safe samples** (1)
    2. **Send all samples** (3)

        >[!NOTE]
        > The **Send safe samples** (1) option means that most samples will be sent automatically. Files that are likely to contain personal information will still prompt and require additional confirmation.

        > [!WARNING]
        > Setting the option to **Always Prompt** (0) will lower the protection state of the device. Setting it to **Never send** (2) means that the [Block at First Sight](configure-block-at-first-sight-microsoft-defender-antivirus.md) feature of Microsoft Defender ATP won't work.

7. Click **OK**.

## Use PowerShell cmdlets to enable cloud-delivered protection

Use the following cmdlets to enable cloud-delivered protection:

```PowerShell
Set-MpPreference -MAPSReporting Advanced
Set-MpPreference -SubmitSamplesConsent SendAllSamples
```

See [Use PowerShell cmdlets to configure and run Microsoft Defender Antivirus](use-powershell-cmdlets-microsoft-defender-antivirus.md) and [Defender cmdlets](https://technet.microsoft.com/library/dn433280.aspx) for more information on how to use PowerShell with Microsoft Defender Antivirus. [Policy CSP - Defender](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-defender) also has more information specifically on [-SubmitSamplesConsent](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-defender#defender-submitsamplesconsent).

>[!NOTE]
> You can also set **-SubmitSamplesConsent** to `SendSafeSamples` (the default setting), `NeverSend`, or `AlwaysPrompt`. The `SendSafeSamples` setting means that most samples will be sent automatically. Files that are likely to contain personal information will still prompt and require additional confirmation.

>[!WARNING]
> Setting **-SubmitSamplesConsent** to `NeverSend` or `AlwaysPrompt` will lower the protection level of the device. In addition, setting it to `NeverSend` means that the [Block at First Sight](configure-block-at-first-sight-microsoft-defender-antivirus.md) feature of Microsoft Defender ATP won't work.

## Use Windows Management Instruction (WMI) to enable cloud-delivered protection

Use the [**Set** method of the **MSFT_MpPreference**](https://msdn.microsoft.com/library/dn439474(v=vs.85).aspx) class for the following properties:

```WMI
MAPSReporting
SubmitSamplesConsent
```

See the following for more information and allowed parameters:

- [Windows Defender WMIv2 APIs](https://msdn.microsoft.com/library/dn439477(v=vs.85).aspx)

## Enable cloud-delivered protection on individual clients with the Windows Security app

> [!NOTE]
> If the **Configure local setting override for reporting Microsoft MAPS** Group Policy setting is set to **Disabled**, then the **Cloud-based protection** setting in Windows Settings will be greyed-out and unavailable. Changes made through a Group Policy Object must first be deployed to individual endpoints before the setting will be updated in Windows Settings.

1. Open the Windows Security app by clicking the shield icon in the task bar or searching the start menu for **Defender**.

2. Click the **Virus & threat protection** tile (or the shield icon on the left menu bar) and then the **Virus & threat protection settings** label:

    ![Screenshot of the Virus & threat protection settings label in the Windows Security app](images/defender/wdav-protection-settings-wdsc.png)

3. Confirm that **Cloud-based Protection** and **Automatic sample submission** are switched to **On**.

>[!NOTE]
>If automatic sample submission has been configured with Group Policy then the setting will be greyed-out and unavailable.

## Related topics

- [Configure the cloud block timeout period](configure-cloud-block-timeout-period-microsoft-defender-antivirus.md)
- [Configure block at first sight](configure-block-at-first-sight-microsoft-defender-antivirus.md)
- [Use PowerShell cmdlets to manage Microsoft Defender Antivirus](use-powershell-cmdlets-microsoft-defender-antivirus.md)
- [Help secure Windows PCs with Endpoint Protection for Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/help-secure-windows-pcs-with-endpoint-protection-for-microsoft-intune)]
- [Defender cmdlets](https://technet.microsoft.com/library/dn433280.aspx)
- [Utilize Microsoft cloud-delivered protection in Microsoft Defender Antivirus](utilize-microsoft-cloud-protection-microsoft-defender-antivirus.md)
- [How to create and deploy antimalware policies: Cloud-protection service](https://docs.microsoft.com/configmgr/protect/deploy-use/endpoint-antimalware-policies#cloud-protection-service)
- [Microsoft Defender Antivirus in Windows 10](microsoft-defender-antivirus-in-windows-10.md)
