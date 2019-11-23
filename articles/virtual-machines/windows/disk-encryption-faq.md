---
title: FAQ - Azure Disk Encryption for Windows VMs
description: This article provides answers to frequently asked questions about Microsoft Azure Disk Encryption for Windows IaaS VMs.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: ea2a66a6b012664a9596a02ea32c1a0b677ee3ea
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384270"
---
# <a name="azure-disk-encryption-for-windows-vms-faq"></a>Azure Disk Encryption for Windows VMs FAQ

This article provides answers to frequently asked questions (FAQ) about Azure Disk Encryption for Windows VMs. For more information about this service, see [Azure Disk Encryption overview](disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Where is Azure Disk Encryption in general availability (GA)?

Azure Disk Encryption is in general availability in all Azure public regions.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>What user experiences are available with Azure Disk Encryption?

Azure Disk Encryption GA supports Azure Resource Manager templates, Azure PowerShell, and Azure CLI. The different user experiences give you flexibility. You have three different options for enabling disk encryption for your VMs. For more information on the user experience and step-by-step guidance available in Azure Disk Encryption, see [Azure Disk Encryption scenarios for Windows](disk-encryption-windows.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>How much does Azure Disk Encryption cost?

There's no charge for encrypting VM disks with Azure Disk Encryption, but there are charges associated with the use of Azure Key Vault. For more information on Azure Key Vault costs, see the [Key Vault pricing](https://azure.microsoft.com/pricing/details/key-vault/) page.

## <a name="how-can-i-start-using-azure-disk-encryption"></a>How can I start using Azure Disk Encryption?

To get started, read the [Azure Disk Encryption overview](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>What VM sizes and operating systems support Azure Disk Encryption?

The [Azure Disk Encryption overview](disk-encryption-overview.md) article lists the [VM sizes](disk-encryption-overview.md#supported-vm-sizes) and [VM operating systems](disk-encryption-overview.md#supported-operating-systems) that support Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Can I encrypt both boot and data volumes with Azure Disk Encryption?

You can encrypt both boot and data volumes, but you can't encrypt the data without first encrypting the OS volume.

After you've encrypted the OS volume, disabling encryption on the OS volume isn't supported.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Can I encrypt an unmounted volume with Azure Disk Encryption?

No, Azure Disk Encryption only encrypts mounted volumes.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>How do I rotate secrets or encryption keys?

To rotate secrets, just call the same command you used originally to enable disk encryption, specifying a different Key Vault. To rotate the key encryption key, call the same command you used originally to enable disk encryption, specifying the new key encryption. 

>[!WARNING]
> - If you have previously used [Azure Disk Encryption with Azure AD app](disk-encryption-windows-aad.md) by specifying Azure AD credentials to encrypt this VM, you will have to continue use this option to encrypt your VM. You can’t use Azure Disk Encryption on this encrypted VM as this isn’t a supported scenario, meaning switching away from AAD application for this encrypted VM isn’t supported yet.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>How do I add or remove a key encryption key if I didn't originally use one?

To add a key encryption key, call the enable command again passing the key encryption key parameter. To remove a key encryption key, call the enable command again without the key encryption key parameter.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Does Azure Disk Encryption allow you to bring your own key (BYOK)?

Yes, you can supply your own key encryption keys. These keys are safeguarded in Azure Key Vault, which is the key store for Azure Disk Encryption. For more information on the key encryption keys support scenarios, see [Creating and configuring a key vault for Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Can I use an Azure-created key encryption key?

Yes, you can use Azure Key Vault to generate a key encryption key for Azure disk encryption use. These keys are safeguarded in Azure Key Vault, which is the key store for Azure Disk Encryption. For more information on the key encryption key, see [Creating and configuring a key vault for Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Can I use an on-premises key management service or HSM to safeguard the encryption keys?

You can't use the on-premises key management service or HSM to safeguard the encryption keys with Azure Disk Encryption. You can only use the Azure Key Vault service to safeguard the encryption keys. For more information on the key encryption key support scenarios, see [Creating and configuring a key vault for Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>What are the prerequisites to configure Azure Disk Encryption?

There are prerequisites for Azure Disk Encryption. See the [Creating and configuring a key vault for Azure Disk Encryption](disk-encryption-key-vault.md) article to create a new key vault, or set up an existing key vault for disk encryption access to enable encryption, and safeguard secrets and keys. For more information on the key encryption key support scenarios, see [Creating and configuring a key vault for Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>What are the prerequisites to configure Azure Disk Encryption with an Azure AD app (previous release)?

There are prerequisites for Azure Disk Encryption. See the [Azure Disk Encryption with Azure AD](disk-encryption-windows-aad.md) content to create an Azure Active Directory application, create a new key vault, or set up an existing key vault for disk encryption access to enable encryption, and safeguard secrets and keys. For more information on the key encryption key support scenarios, see [Creating and configuring a key vault for Azure Disk Encryption with Azure AD](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Is Azure Disk Encryption using an Azure AD app (previous release) still supported?
Sim. Disk encryption using an Azure AD app is still supported. However, when encrypting new VMs it's recommended that you use the new method rather than encrypting with an Azure AD app. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Can I migrate VMs that were encrypted with an Azure AD app to encryption without an Azure AD app?
  Currently, there isn't a direct migration path for machines that were encrypted with an Azure AD app to encryption without an Azure AD app. Additionally, there isn't a direct path from encryption without an Azure AD app to encryption with an AD app. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>What version of Azure PowerShell does Azure Disk Encryption support?

Use the latest version of the Azure PowerShell SDK to configure Azure Disk Encryption. Download the latest version of [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption is *not* supported by Azure SDK version 1.1.0.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>What is the disk "Bek Volume" or "/mnt/azure_bek_disk"?

The "Bek volume" is a local data volume that securely stores the encryption keys for Encrypted Azure VMs.

> [!NOTE]
> Do not delete or edit any contents in this disk. Do not unmount the disk since the encryption key presence is needed for any encryption operations on the IaaS VM.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>What encryption method does Azure Disk Encryption use?

Azure Disk Encryption selects the encryption method in BitLocker based on the version of Windows as follows:

| Windows Versions                 | Versão | Encryption Method        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012, Windows 10, or greater  | >=1511 |XTS-AES 256 bit           |
| Windows Server 2012, Windows 8, 8.1, 10 | < 1511 |AES 256 bit *              |
| Windows Server 2008R2            |        |AES 256 bit with Diffuser |

\* AES 256 bit with Diffuser isn't supported in Windows 2012 and later.

To determine Windows OS version, run the 'winver' tool in your virtual machine.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>If I use EncryptFormatAll and specify all volume types, will it erase the data on the data drives that we already encrypted?
No, data won't be erased from data drives that are already encrypted using Azure Disk Encryption. Similar to how EncryptFormatAll didn't re-encrypt the OS drive, it won't re-encrypt the already encrypted data drive. 

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Can I backup and restore an encrypted VM? 

Azure Backup provides a mechanism to backup and restore encrypted VM's within the same subscription and region.  For instructions, please see [Back up and restore encrypted virtual machines with Azure Backup](../../backup/backup-azure-vms-encryption.md).  Restoring an encrypted VM to a different region is not currently supported.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Where can I go to ask questions or provide feedback?

You can ask questions or provide feedback on the [Azure Disk Encryption forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Passos seguintes
In this document, you learned more about the most frequent questions related to Azure Disk Encryption. For more information about this service, see the following articles:

- [Descrição geral do Azure Disk Encryption](disk-encryption-overview.md)
- [Apply disk encryption in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure data encryption at rest](../../security/fundamentals/encryption-atrest.md)
