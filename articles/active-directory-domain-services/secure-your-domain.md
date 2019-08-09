---
title: Proteger seu Azure Active Directory Domain Services domínio gerenciado | Microsoft Docs
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
ms.openlocfilehash: 923ecae9dc649b8f5cdcfd447b78fdec0805927a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879157"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Proteger seu Azure AD Domain Services domínio gerenciado
Este artigo ajuda você a proteger seu domínio gerenciado. Você pode desativar o uso de pacotes de criptografia fraca e desabilitar a sincronização de hash de credencial NTLM.

## <a name="install-the-required-powershell-modules"></a>Instalar os módulos necessários do PowerShell

### <a name="install-and-configure-azure-ad-powershell"></a>Instalar e configurar o PowerShell do Azure AD
Siga as instruções no artigo para [instalar o módulo do PowerShell do Azure AD e conectar-se ao Azure ad](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instalar e configurar o Azure PowerShell
Siga as instruções no artigo para [instalar o módulo Azure PowerShell e conectar-se à sua assinatura do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>Desabilitar pacotes de criptografia fracos e sincronização de hash de credencial NTLM
Use o seguinte script do PowerShell para:

1. Desativar o suporte para NTLM v1 no domínio gerido.
2. Desabilite a sincronização de hashes de senha NTLM do seu AD local.
3. Desative o TLS v1 no domínio gerido.

Se você receber um erro com o `Get-AzResource` comando que o recurso *Microsoft. AAD/nome_do_domínioservices* não existe, [eleve o seu acesso para gerenciar todas as assinaturas e grupos de gerenciamento do Azure](../role-based-access-control/elevate-access-global-admin.md).

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
> Os usuários (e as contas de serviço) não poderão executar associações LDAP simples se você tiver desabilitado a sincronização de hash de senha NTLM em sua instância de Azure AD Domain Services.  Para obter mais informações sobre como desabilitar a sincronização de hash de senha NTLM, leia [proteger seu domínio gerenciado dos serviços de domínio do Azure ad](secure-your-domain.md).
>
>

## <a name="next-steps"></a>Passos Seguintes
* [Entender a sincronização no Azure AD Domain Services](synchronization.md)
