---
title: Azure AD Connect agente de provisão de nuvem gMSA PowerShell cmdlets
description: Saiba como utilizar o agente de provisão de nuvem Azure AD Connect gMSA.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 411a8e46151a762bcd270fb676f78a91f760ac4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653801"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-gmsa-powershell-cmdlets"></a>Azure AD Connect agente de provisão de nuvem gMSA PowerShell cmdlets

O objetivo deste documento é descrever o agente de provisão de nuvem Azure AD Connect gMSA PowerShell cmdlets. Estes cmdlets permitem-lhe ter mais granularidade nas permissões que são aplicadas na conta de serviço (gmsa). Por predefinição, o Azure AD Connect cloud sync aplica todas as permissões semelhantes ao Azure AD Connect no gmsa predefinido ou num gmsa personalizado. 

Este documento abrangerá os seguintes cmdlets:  

`Set-AADCloudSyncRestrictedPermissions`

`Ste-AADCloudSyncPermissions` 

## <a name="how-to-use-the-cmdlets"></a>Como utilizar os cmdlets:  

São necessários os seguintes pré-requisitos para a utilização destes cmdlets.

1. Instale o agente de provisionamento. 
2. Módulo PS do Agente de Provisionamento de Importação numa sessão PowerShell. 

 ```PowerShell
 Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll"  
 ```
3. Remova as permissões existentes.  Para remover todas as permissões existentes na conta de serviço, exceto a utilização self: `Set-AADCloudSyncRestrictedPermission` .  

    Este cmdlet requer um parâmetro chamado `Credential` que pode ser passado, ou irá solicitar se for chamado sem ele.

    Para criar um uso variável  

   `$credential = Get-Credential` 

   Isto irá levar o utilizador a introduzir o nome de utilizador e a palavra-passe. As credenciais devem estar num administrador de domínio mínimo (do domínio onde o agente está instalado), podendo ser também administradora da empresa. 

4.  Em seguida, pode ligar para o cmdlet para remover permissões adicionais: 
   ```PowerShell
   Set-AADCloudSyncRestrictedPermissions -Credential $credential 
   ```
5. Ou pode simplesmente ligar 

   `Set-AADCloudSyncRestrictedPermissions` o que vai pedir credenciais. 

 6.  Adicione o tipo de permissão específica.  As permissões adicionadas são as mesmas que a Azure AD Connect.  Consulte [a utilização de set-AADCloudSyncPermissions](#using-set-aadcloudsyncpermissions) abaixo, por exemplo, na definição das permissões.

## <a name="using-set-aadcloudsyncpermissions"></a>Usando Set-AADCloudSyncPermissions 
`Set-AADCloudSyncPermissions` suporta os seguintes tipos de permissão que são idênticos às permissões utilizadas pelo Azure AD Connect. São suportados os seguintes tipos de permissão: 

|Tipo de permissão|Description|
|-----|-----|
|BasicRead| Consulte permissões [BasicRead](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#configure-basic-read-only-permissions) para Azure AD Connect|
|PasswordHashSync|Consulte permissões [PasswordHashSync](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-hash-synchronization) para Azure AD Connect|
|PasswordWriteBack|Consulte permissões [PasswordWriteBack](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-writeback) para Azure AD Connect|
|HybridExchangePermissions|Ver [permissões híbridasExchangePermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-hybrid-deployment) para Azure AD Connect| 
|ExchangeMailPublicFolderPermissions| Ver [Permissão de Permissão de Permissão de Permissions ExchangeMailMailPublicFolder](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-mail-public-folders-preview) para Azure AD Connect| 
|CloudHR| Aplica 'Controlo total' em 'Objetos de utilizador descendentes' e 'Criar/eliminar objetos do Utilizador' em 'Este objeto e todos os objetos descendentes'| 
|Todos|adiciona todas as permissões acima.| 



Pode utilizar as sesionações AADCloudSync de uma de duas maneiras:
- [Conceda uma certa permissão a todos os domínios configurados](#grant-a-certain-permission-to-all-configured-domains) 
- [Conceder uma certa permissão a um domínio específico](#grant-a-certain-permission-to-a-specific-domain) 
## <a name="grant-a-certain-permission-to-all-configured-domains"></a>Conceda uma certa permissão a todos os domínios configurados 
A concessão de certas permissões a todos os domínios configurados exigirá a utilização de uma conta administrada da empresa.


 ```PowerShell
Set-AADCloudSyncPermissions -PermissionType “Any mentioned above” -EACredential $credential (prepopulated same as above [$credential = Get-Credential]) 
```

## <a name="grant-a-certain-permission-to-a-specific-domain"></a>Conceder uma certa permissão a um domínio específico 
Conceder determinadas permissões a um domínio específico exigirá a utilização, no mínimo, de uma conta de administração de domínio do domínio que está a tentar adicionar.


 ```PowerShell
Set-AADCloidSyncPermissions -PermissionType “Any mentioned above” -TargetDomain “FQDN of domain” (has to be already configured through wizard) -TargetDomaincredential $credential(same as above) 
```
 

Nota: para 1. As credenciais devem estar no mínimo administração da Enterprise. 

Para 2. As Credenciais podem ser administradas por domínio ou administração de empresas. 

  

