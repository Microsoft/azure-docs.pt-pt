---
title: Proteger o seu domínio gerido do Azure Active Directory Domain Services | Documentos da Microsoft
description: Proteja o seu domínio gerido
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: e94cd9ca049cfdfd2321ce046714506ed1f23390
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483285"
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

> [!IMPORTANT]
> Os utilizadores (e contas de serviço) não é possível efetuar une simples de LDAP se tiver desativado a sincronização de hash de palavra-passe NTLM na sua instância do Azure AD Domain Services.  Para obter mais informações sobre como desabilitar a sincronização de hash de palavra-passe NTLM, leia [proteger o seu domínio gerido do Azure AD DOmain Services](secure-your-domain.md).
>
>

## <a name="next-steps"></a>Passos Seguintes
* [Compreender a sincronização no Azure AD Domain Services](synchronization.md)
