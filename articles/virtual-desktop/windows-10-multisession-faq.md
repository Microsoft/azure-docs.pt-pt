---
title: Windows 10 Enterprise multi-session FAQ - Azure
description: Frequently asked questions and best practices for using Windows 10 Enterprise multi-session for Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: helohr
ms.openlocfilehash: 10724407b8ba5568b38a844f2bf475060e2b7699
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227671"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>FAQ de multissessões do Windows 10 Enterprise

This article will answer frequently asked questions and cover best practices for Windows 10 Enterprise multi-session.
 
## <a name="what-is-windows-10-enterprise-multi-session"></a>What is Windows 10 Enterprise multi-session? 

Windows 10 Enterprise multi-session, formerly known as Windows 10 Enterprise for Virtual Desktops (EVD), is a new Remote Desktop Session Host that allows multiple concurrent interactive sessions, which previously only Windows Server could do. This capability gives users a familiar Windows 10 experience while IT can benefit from the cost advantages of multi-session and use existing per-user Windows licensing instead of RDS Client Access Licenses (CALs). For more information about licenses and pricing, see [Windows Virtual Desktop pricing](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>How many users can simultaneously have an interactive session on Windows 10 Enterprise multi-session?

How many interactive sessions that can be active at the same time relies on your system's hardware resources (vCPU, memory, disk, and vGPU), how your users use their apps while signed in to a session, and how heavy your system's workload is. We suggest you validate your system's performance to understand how many users you can have on Windows 10 Enterprise multi-session. To learn more, see [Windows Virtual Desktop pricing](https://azure.microsoft.com/pricing/details/virtual-desktop/). 
 
## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>Why does my application report Windows 10 Enterprise multi-session as a Server operating system?

Windows 10 Enterprise multi-session is a virtual edition of Windows 10 Enterprise. One of the differences is that this operating system (OS) reports the [ProductType](https://docs.microsoft.com/windows/desktop/cimwin32prov/win32-operatingsystem) as having a value of 3, the same value as Windows Server. This property keeps the OS compatible with existing RDSH management tooling, RDSH multi-session-aware applications, and mostly low-level system performance optimizations for RDSH environments. Some application installers can block installation on Windows 10 multi-session depending on whether they detect the ProductType is set to Client. If your app won't install, contact your application vendor for an updated version. 
 
## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Can I run Windows 10 Enterprise multi-session on-premises?

Windows 10 Enterprise multi-session can't run in on-premises production environments because it's optimized for the Windows Virtual Desktop service for Azure. It’s against the licensing agreement to run Windows 10 Enterprise multi-session outside of Azure for production purposes. Windows 10 Enterprise multi-session won't activate against on-premises Key Management Services (KMS).
 
## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>How do I customize the Windows 10 Enterprise multi-session image for my organization?

You can start a virtual machine (VM) in Azure with Windows 10 Windows 10 Enterprise multi-session and customize it by installing LOB applications, sysprep/generalize, and then create an image using the Azure portal.  
 
To get started, create a VM in Azure with Windows 10 Windows 10 Enterprise multi-session. Instead of starting the VM in Azure, you can download the VHD directly. After that, you'll be able to use the VHD you downloaded to create a new Generation 1 VM on a Windows 10 PC with Hyper-V enabled.

Customize the image to your needs by installing LOB applications and sysprep the image. When you're done customizing, upload the image to Azure with the VHD inside. After that, get Windows Virtual Desktop from the Azure Marketplace and use it to to deploy a new host pool with the customized image.
 
## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>How do I manage Windows 10 Enterprise multi-session after deployment?

You can use any supported configuration tool, but we recommend System Center Configuration Manager 1906 because it supports Windows 10 Enterprise multi-session. We're currently working on Microsoft Intune support.
 
## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Can Windows 10 Enterprise multi-session be Azure Active Directory (AD)-joined?

Windows 10 Enterprise multi-session is currently supported to be hybrid Azure AD-joined. After Windows 10 Enterprise multi-session is domain-joined, use the existing Group Policy Object to enable Azure AD registration. For more information, see [Plan your hybrid Azure Active Directory join implementation](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan).
 
## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Where can I find the Windows 10 Enterprise multi-session image?

Windows 10 Enterprise multi-session is in the Azure gallery. To find it, navigate to the Azure portal and search for the Windows 10 Enterprise for Virtual Desktops release. For an image integrated with Office Pro Plus, go to the Azure portal and search for Microsoft Windows 10 + Office 365 ProPlus.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>Which Windows 10 Enterprise multi-session image should I use?

The Azure gallery has several releases, including Windows 10 Enterprise multi-session, version 1809, and Windows 10 Enterprise multi-session, version 1903. We recommend using the latest version for improved performance and reliability.
 
## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>Which Windows 10 Enterprise multi-session versions are supported?

Windows 10 Enterprise multi-session, versions 1809 and later are supported and are available in the Azure gallery. These releases follow the same support life-cycle policy as Windows 10 Enterprise, which means the spring release is supported for 18 months and the fall release for 30 months.
 
## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Which profile management solution should I use for Windows 10 Enterprise multi-session?

We recommend you use FSLogix profile containers when you configure Windows 10 Enterprise in non-persistent environments or other scenarios that need a centrally stored profile. FSLogix ensures the user profile is available and up-to-date for every user session. We also recommend you use your FSLogix profile container to store a user profile in any SMB share with appropriate permissions, but you can store user profiles in Azure page blob storage if necessary. Windows Virtual Desktop users can use FSLogix at no additional cost.
 
For more information about how to configure an FSLogix profile container, see [Configure the FSLogix profile container](create-host-pools-user-profile.md#configure-the-fslogix-profile-container).  

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Which license do I need to access Windows 10 Enterprise multi-session?

For a full list of applicable licenses, see [Windows Virtual Desktop pricing](https://azure.microsoft.com/pricing/details/virtual-desktop/).
 
## <a name="next-steps"></a>Passos seguintes

To learn more about Windows Virtual Desktop and Windows 10 Enterprise multi-session:

- Read our [Windows Virtual Desktop Preview documentation](overview.md)
- Visit our [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Set up your Windows Virtual Desktop deployment with the [Windows Virtual Desktop tutorials](tenant-setup-azure-active-directory.md)
