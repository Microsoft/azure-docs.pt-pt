---
title: Conceder permissão a muitos aplicativos para acessar um cofre de chaves do Azure-Azure Key Vault | Microsoft Docs
description: Saiba como conceder permissão a muitos aplicativos para acessar um cofre de chaves
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 07ee544057ffeb0a5859cc771b124523ec79c9c0
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976407"
---
# <a name="grant-several-applications-access-to-a-key-vault"></a>Conceder a vários aplicativos acesso a um cofre de chaves

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A política de controle de acesso pode ser usada para conceder acesso a um cofre de chaves a vários aplicativos. Uma política de controle de acesso pode dar suporte a até 1024 aplicativos e é configurada da seguinte maneira:

1. Crie um grupo de segurança Azure Active Directory. 
2. Adicione todas as entidades de serviço associadas do aplicativo ao grupo de segurança.
3. Conceda ao grupo de segurança acesso ao seu Key Vault.

## <a name="prerequisites"></a>Pré-requisitos

Aqui estão os pré-requisitos:
* [Instalar o Azure PowerShell](/powershell/azure/overview).
* [Instale o módulo Azure Active Directory v2 do PowerShell](https://www.powershellgallery.com/packages/AzureAD).
* Permissões para criar/editar grupos no locatário Azure Active Directory. Se você não tiver permissões, talvez seja necessário entrar em contato com o administrador do Azure Active Directory. Consulte [sobre Azure Key Vault chaves, segredos e certificados](about-keys-secrets-and-certificates.md) para obter detalhes sobre Key Vault permissões de política de acesso.

## <a name="granting-key-vault-access-to-applications"></a>Concedendo acesso Key Vault a aplicativos

Execute os seguintes comandos no PowerShell:

```powershell
# Connect to Azure AD 
Connect-AzureAD 
 
# Create Azure Active Directory Security Group 
$aadGroup = New-AzureADGroup -Description "Contoso App Group" -DisplayName "ContosoAppGroup" -MailEnabled 0 -MailNickName none -SecurityEnabled 1 
 
# Find and add your applications (ServicePrincipal ObjectID) as members to this group 
$spn = Get-AzureADServicePrincipal –SearchString "ContosoApp1" 
Add-AzureADGroupMember –ObjectId $aadGroup.ObjectId -RefObjectId $spn.ObjectId 
 
# You can add several members to this group, in this fashion. 
 
# Set the Key Vault ACLs 
Set-AzKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId `
-PermissionsToKeys decrypt,encrypt,unwrapKey,wrapKey,verify,sign,get,list,update,create,import,delete,backup,restore,recover,purge `
–PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge `
–PermissionsToCertificates get,list,delete,create,import,update,managecontacts,getissuers,listissuers,setissuers,deleteissuers,manageissuers,recover,purge,backup,restore `
-PermissionsToStorage get,list,delete,set,update,regeneratekey,getsas,listsas,deletesas,setsas,recover,backup,restore,purge 
 
# Of course you can adjust the permissions as required 
```

Se você precisar conceder um conjunto diferente de permissões a um grupo de aplicativos, crie um grupo de segurança Azure Active Directory separado para esses aplicativos.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre como [proteger seu cofre de chaves](key-vault-secure-your-key-vault.md).
