---
title: Proteger o seu domínio gerido do Azure Active Directory Domain Services | Documentos da Microsoft
description: Proteja o seu domínio gerido
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: ab371553a96f3a8d393c8b773c4024d04fd171a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66246738"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Proteger o seu domínio gerido do Azure AD Domain Services
Este artigo ajuda-o a proteger o seu domínio gerido. Pode desativar a utilização de conjuntos de cifras fraco e desativar a sincronização de hashes de credencial NTLM.

## <a name="install-the-required-powershell-modules"></a>Instalar os módulos necessários do PowerShell

### <a name="install-and-configure-azure-ad-powershell"></a>Instalar e configurar o Azure AD PowerShell
Siga as instruções no artigo para [instalar o módulo Azure AD PowerShell e ligar ao Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instalar e configurar o Azure PowerShell
Siga as instruções no artigo para [instalar o módulo Azure PowerShell e ligue à sua subscrição do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>Desativar a conjuntos de cifras fraco e sincronização de hashes de credencial NTLM
Utilize o seguinte script do PowerShell para:
1. Desativar o suporte para NTLM v1 no domínio gerido.
2. Desativar a sincronização de hashes de palavra-passe NTLM do seu local de AD.
3. Desative o TLS v1 no domínio gerido.

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

## <a name="next-steps"></a>Passos Seguintes
* [Compreender a sincronização no Azure AD Domain Services](synchronization.md)
