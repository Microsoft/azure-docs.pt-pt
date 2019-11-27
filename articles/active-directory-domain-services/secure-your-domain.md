---
title: Proteger Azure AD Domain Services | Microsoft Docs
description: Saiba como desabilitar codificações fracas, protocolos antigos e sincronização de hash de senha NTLM para um domínio gerenciado Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 8eee516beaaf26ed25bd20f9689d26fdb1eb9b40
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546228"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-ad-domain-services-managed-domain"></a>Desabilitar codificações fracas e a sincronização de hash de senha para proteger um Azure AD Domain Services domínio gerenciado

Por padrão, Azure Active Directory Domain Services (Azure AD DS) permite o uso de codificações como NTLM v1 e TLS v1. Essas codificações podem ser necessárias para alguns aplicativos herdados, mas são consideradas fracas e podem ser desabilitadas se você não precisar delas. Se você tiver conectividade híbrida local usando Azure AD Connect, também poderá desabilitar a sincronização de hashes de senha NTLM.

Este artigo mostra como desabilitar as codificações NTLM v1 e TLS v1 e desabilitar a sincronização de hash de senha NTLM.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa dos seguintes recursos:

* Uma subscrição ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário Azure Active Directory associado à sua assinatura, seja sincronizado com um diretório local ou um diretório somente em nuvem.
    * Se necessário, [crie um locatário Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um Azure Active Directory Domain Services domínio gerenciado habilitado e configurado em seu locatário do Azure AD.
    * Se necessário, [crie e configure uma instância de Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Instalar e configurar o Azure PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo Azure PowerShell e conectar-se à sua assinatura do Azure](/powershell/azure/install-az-ps).
    * Certifique-se de entrar em sua assinatura do Azure usando o cmdlet [Connect-AzAccount][Connect-AzAccount] .
* Instalar e configurar o PowerShell do Azure AD.
    * Se necessário, siga as instruções para [instalar o módulo do PowerShell do Azure AD e conectar-se ao Azure ad](/powershell/azure/active-directory/install-adv2).
    * Certifique-se de entrar no seu locatário do Azure AD usando o cmdlet [Connect-AzureAD][Connect-AzureAD] .

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Desabilitar codificações fracas e sincronização de hash de senha NTLM

Para desabilitar conjuntos de criptografia de baixa segurança e sincronização de hash de credencial NTLM, entre em sua conta do Azure e obtenha o recurso de AD DS do Azure usando o cmdlet [Get-AzResource][Get-AzResource] :

> [!TIP]
> Se você receber um erro usando o comando [Get-AzResource][Get-AzResource] que o recurso *Microsoft. AAD/DomainServices* não existe, [eleve seu acesso para gerenciar todas as assinaturas e grupos de gerenciamento do Azure][global-admin].

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Em seguida, defina *DomainSecuritySettings* para configurar as seguintes opções de segurança:

1. Desabilite o suporte a NTLM v1.
2. Desabilite a sincronização de hashes de senha NTLM do seu AD local.
3. Desabilite o TLS v1.

> [!IMPORTANT]
> Os usuários e as contas de serviço não poderão executar associações LDAP simples se você desabilitar a sincronização de hash de senha NTLM no domínio gerenciado AD DS do Azure. Se você precisar executar associações LDAP simples, não defina a opção de configuração de segurança *"SyncNtlmPasswords" = "Disabled";* no comando a seguir.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

Por fim, aplique as configurações de segurança definidas ao domínio gerenciado AD DS do Azure usando o cmdlet [set-AzResource][Set-AzResource] . Especifique o recurso de AD DS do Azure na primeira etapa e as configurações de segurança da etapa anterior.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

Leva alguns minutos para que as configurações de segurança sejam aplicadas ao domínio gerenciado AD DS do Azure.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o processo de sincronização, confira [como os objetos e as credenciais são sincronizados em um domínio gerenciado do Azure AD DS][synchronization].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD