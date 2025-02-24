---
title: Microsoft Defender Antivirus on Windows Server 2016 and 2019
description: Enable and configure Windows Defender AV on Windows Server 2016 and 2019 
keywords: windows defender, server, scep, system center endpoint protection, server 2016, current branch, server 2012
search.product: eADQiWindows 10XVcnh
ms.pagetype: security
ms.prod: w10
ms.mktglfcycl: manage
ms.sitesec: library
ms.pagetype: security
ms.localizationpriority: medium
author: denisebmsft
ms.author: deniseb
ms.date: 02/25/2020
ms.reviewer: 
manager: dansimp
---

# Microsoft Defender Antivirus on Windows Server 2016 and 2019

**Applies to:**

- [Microsoft Defender Advanced Threat Protection (Microsoft Defender ATP)](https://go.microsoft.com/fwlink/p/?linkid=2069559)

Microsoft Defender Antivirus is available on Windows Server 2016 and Windows Server 2019. In some instances, Microsoft Defender Antivirus is referred to as Endpoint Protection; however, the protection engine is the same.

While the functionality, configuration, and management are largely the same for Microsoft Defender Antivirus on Windows 10, there are a few key differences on Windows Server 2016 or Windows Server 2019:

- In Windows Server, [automatic exclusions](configure-server-exclusions-microsoft-defender-antivirus.md) are applied based on your defined Server Role.
- In Windows Server, Microsoft Defender Antivirus does not automatically disable itself if you are running another antivirus product.

## The process at a glance

The process of setting up and running Microsoft Defender Antivirus on a server platform includes several steps:

1. [Enable the interface](#enable-the-user-interface-on-windows-server-2016-or-2019)

2. [Install Microsoft Defender Antivirus](#install-microsoft-defender-antivirus-on-windows-server-2016-or-2019)

2. [Verify Microsoft Defender Antivirus is running](#verify-microsoft-defender-antivirus-is-running)

3. [Update your antimalware Security intelligence](#update-antimalware-security-intelligence)

4. (As needed) [Submit samples](#submit-samples)

5. (As needed) [Configure automatic exclusions](#configure-automatic-exclusions)

6. (Only if necessary) [Uninstall Microsoft Defender Antivirus](#need-to-uninstall-microsoft-defender-antivirus)

## Enable the user interface on Windows Server 2016 or 2019

By default, Microsoft Defender Antivirus is installed and functional on Windows Server 2016 and Windows Server 2019. The user interface (GUI) is installed by default on some SKUs, but is not required because you can use PowerShell or other methods to manage Microsoft Defender Antivirus. And if the GUI is not installed on your server, you can add it by using the Add Roles and Features Wizard or PowerShell.

### Turn on the GUI using the Add Roles and Features Wizard

1. Refer to [this article](https://docs.microsoft.com/windows-server/administration/server-manager/install-or-uninstall-roles-role-services-or-features#install-roles-role-services-and-features-by-using-the-add-roles-and-features-wizard), and use the **Add Roles and Features Wizard**.

2. When you get to the **Features** step of the wizard, under **Windows Defender Features**, select the **GUI for Windows Defender** option.

In Windows Server 2016, the **Add Roles and Features Wizard** looks like this:

![Add roles and feature wizard showing the GUI for Windows Defender option](images/server-add-gui.png)

In Windows Server 2019, the **Add Roles and Feature Wizard** looks like this:

![Add roles and features wizard Windows Server 2019](images/WDAV-WinSvr2019-turnfeatureson.jpg)

### Turn on the GUI using PowerShell

The following PowerShell cmdlet will enable the interface: 

```PowerShell
Install-WindowsFeature -Name Windows-Defender-GUI
```

## Install Microsoft Defender Antivirus on Windows Server 2016 or 2019

You can use either the **Add Roles and Features Wizard** or PowerShell to install Microsoft Defender Antivirus.

### Use the Add Roles and Features Wizard

1. Refer to [this article](https://docs.microsoft.com/windows-server/administration/server-manager/install-or-uninstall-roles-role-services-or-features#install-roles-role-services-and-features-by-using-the-add-roles-and-features-wizard), and use the **Add Roles and Features Wizard**.

2. When you get to the **Features** step of the wizard, select the Microsoft Defender Antivirus option. Also select the **GUI for Windows Defender** option.

### Use PowerShell

To use PowerShell to install Microsoft Defender Antivirus, run the following cmdlet:

```PowerShell
Install-WindowsFeature -Name Windows-Defender
```

Event messages for the antimalware engine included with Microsoft Defender Antivirus can be found in [Windows Defender AV Events](troubleshoot-microsoft-defender-antivirus.md).


## Verify Microsoft Defender Antivirus is running

To verify that Microsoft Defender Antivirus is running on your server, run the following PowerShell cmdlet:

```PowerShell
Get-Service -Name windefend
```

To verify that firewall protection is turned on, run the following PowerShell cmdlet:

```PowerShell 
Get-Service -Name mpssvc
```

As an alternative to PowerShell, you can use Command Prompt to verify that Microsoft Defender Antivirus is running. To do that, run the following command from a command prompt: 

```DOS
sc query Windefend
```

The `sc query` command returns information about the Microsoft Defender Antivirus service. When Microsoft Defender Antivirus is running, the `STATE` value displays `RUNNING`.

## Update antimalware Security intelligence 

In order to get updated antimalware Security intelligence, you must have the Windows Update service running. If you use an update management service, like Windows Server Update Services (WSUS), make sure that updates for Microsoft Defender Antivirus Security intelligence are approved for the computers you manage.

By default, Windows Update does not download and install updates automatically on Windows Server 2016 or 2019. You can change this configuration by using one of the following methods:


|Method  |Description  |
|---------|---------|
|**Windows Update** in Control Panel     |- **Install updates automatically** results in all updates being automatically installed, including Windows Defender Security intelligence updates. <br/>- **Download updates but let me choose whether to install them** allows Windows Defender to download and install Security intelligence updates automatically, but other updates are not automatically installed.       |
|**Group Policy**     | You can set up and manage Windows Update by using the settings available in Group Policy, in the following path: **Administrative Templates\Windows Components\Windows Update\Configure Automatic Updates**         |
|The **AUOptions** registry key     |The following two values allow Windows Update to automatically download and install Security intelligence updates: <br/>- **4** Install updates automatically. This value results in all updates being automatically installed, including Windows Defender Security intelligence updates. <br/>- **3** Download updates but let me choose whether to install them.  This value allows Windows Defender to download and install Security intelligence updates automatically, but other updates are not automatically installed.         |

To ensure that protection from malware is maintained, we recommend that you enable the following services:

- Windows Error Reporting service

- Windows Update service

The following table lists the services for Microsoft Defender Antivirus and the dependent services.

|Service Name|File Location|Description|
|--------|---------|--------|
|Windows Defender Service (WinDefend)|`C:\Program Files\Windows Defender\MsMpEng.exe`|This is the main Microsoft Defender Antivirus service that needs to be running at all times.|
|Windows Error Reporting Service (Wersvc)|`C:\WINDOWS\System32\svchost.exe -k WerSvcGroup`|This service sends error reports back to Microsoft.|
|Windows Defender Firewall (MpsSvc)|`C:\WINDOWS\system32\svchost.exe -k LocalServiceNoNetwork`|We recommend leaving the Windows Defender Firewall service enabled.|
|Windows Update (Wuauserv)|`C:\WINDOWS\system32\svchost.exe -k netsvcs`|Windows Update is needed to get Security intelligence updates and antimalware engine updates|

## Submit samples

Sample submission allows Microsoft to collect samples of potentially malicious software. To help provide continued and up-to-date protection, Microsoft researchers use these samples to analyze suspicious activities and produce updated antimalware Security intelligence. We collect program executable files, such as .exe files and .dll files. We do not collect files that contain personal data, like Microsoft Word documents and PDF files.

### Submit a file

1. Review the [submission guide](https://docs.microsoft.com/windows/security/threat-protection/intelligence/submission-guide).

2. Visit the [sample submission portal](https://www.microsoft.com/wdsi/filesubmission), and submit your file.


### Enable automatic sample submission

To enable automatic sample submission, start a Windows PowerShell console as an administrator, and set the **SubmitSamplesConsent** value data according to one of the following settings:

|Setting  |Description  |
|---------|---------|
|**0** Always prompt     |The Microsoft Defender Antivirus service prompts you to confirm submission of all required files. This is the default setting for Microsoft Defender Antivirus, but is not recommended for installations on Windows Server 2016 or 2019 without a GUI.         |
|**1** Send safe samples automatically     |The Microsoft Defender Antivirus service sends all files marked as "safe" and prompts for the remainder of the files.         |
|**2** Never send      |The Microsoft Defender Antivirus service does not prompt and does not send any files.         |
|**3** Send all samples automatically     |The Microsoft Defender Antivirus service sends all files without a prompt for confirmation.         |

## Configure automatic exclusions

To help ensure security and performance, certain exclusions are automatically added based on the roles and features you install when using Microsoft Defender Antivirus on Windows Server 2016 or 2019.

See [Configure exclusions in Microsoft Defender Antivirus on Windows Server](configure-server-exclusions-microsoft-defender-antivirus.md). 

## Need to uninstall Microsoft Defender Antivirus?

If you are using a third-party antivirus solution and you're running into issues with that solution and Microsoft Defender Antivirus, you can consider uninstalling Microsoft Defender Antivirus. Before you do that, review the following resources:

- See the question "Should I run Microsoft security software at the same time as other security products?" on the [Windows Defender Security Intelligence Antivirus and antimalware software FAQ](https://www.microsoft.com/wdsi/help/antimalware-faq#multiple-products).

- See [Better together: Microsoft Defender Antivirus and Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-antivirus/why-use-microsoft-antivirus). This article describes 10 advantages to using Microsoft Defender Antivirus together with Microsoft Defender Advanced Threat Protection.

If you determine you do want to uninstall Microsoft Defender Antivirus, follow the steps in the following sections.

### Uninstall Microsoft Defender Antivirus using the Remove Roles and Features wizard

1. Refer to [this article](https://docs.microsoft.com/windows-server/administration/server-manager/install-or-uninstall-roles-role-services-or-features#remove-roles-role-services-and-features-by-using-the-remove-roles-and-features-wizard), and use the **Remove Roles and Features Wizard**. 

2. When you get to the **Features** step of the wizard, unselect the **Windows Defender Features** option. 

    If you unselect **Windows Defender** by itself under the **Windows Defender Features** section, you will be prompted to remove the interface option **GUI for Windows Defender**. 
    
    Windows Defender AV will still run normally without the user interface, but the user interface cannot be enabled if you disable the core **Windows Defender** feature.

### Uninstall Microsoft Defender Antivirus using PowerShell

>[!NOTE]
>You can't uninstall the Windows Security app, but you can disable the interface with these instructions.

The following PowerShell cmdlet will also uninstall Windows Defender AV on Windows Server 2016 or 2019:

```PowerShell
Uninstall-WindowsFeature -Name Windows-Defender
```

### Turn off the GUI using PowerShell

To turn off the Microsoft Defender Antivirus GUI, use the following PowerShell cmdlet:

```PowerShell
Uninstall-WindowsFeature -Name Windows-Defender-GUI
```


## Related topics

- [Microsoft Defender Antivirus in Windows 10](microsoft-defender-antivirus-in-windows-10.md)

- [Configure exclusions in Windows Defender AV on Windows Server](configure-server-exclusions-microsoft-defender-antivirus.md) 


