---
title: Serviços de Domínio AD Secure Azure [ Microsoft Docs
description: Aprenda a desativar cifras fracas, protocolos antigos e sincronização de hash de senha NTLM para um domínio gerido pelo Azure Ative Directory Domain Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74546228"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-ad-domain-services-managed-domain"></a>Desative cifras fracas e sincronização de hash de senha para garantir um domínio gerido por Serviços de Domínio Azure AD

Por predefinição, os Serviços de Domínio ativo do Azure (Azure AD DS) permitem a utilização de cifras como ntLM v1 e TLS v1. Estas cifras podem ser necessárias para algumas aplicações antigas, mas são consideradas fracas e podem ser desativadas se não precisar delas. Se tiver conectividade híbrida no local utilizando o Azure AD Connect, também pode desativar a sincronização dos hashes de senha NTLM.

Este artigo mostra-lhe como desativar as cifras ntLM v1 e TLS v1 e desativar a sincronização de hash de senha NTLM.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo, precisa dos seguintes recursos:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
    * Se necessário, crie e configure uma instância de Serviços de [Domínio de Diretório Ativo Azure][create-azure-ad-ds-instance].
* Instalar e configurar o Azure PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo PowerShell Azure e ligue-se à sua subscrição Azure](/powershell/azure/install-az-ps).
    * Certifique-se de que insere a sua subscrição Azure utilizando o cmdlet [Connect-AzAccount.][Connect-AzAccount]
* Instale e configure o Azure AD PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo PowerShell Azure AD e ligue-se ao Azure AD](/powershell/azure/active-directory/install-adv2).
    * Certifique-se de que inscreveu o seu inquilino Azure AD utilizando o cmdlet [Connect-AzureAD.][Connect-AzureAD]

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Desativar cifras fracas e sincronização de hash de senha NTLM

Para desativar as fracas suítes cifras e a sincronização de haxixe credencial NTLM, inscreva-se na sua conta Azure e, em seguida, obtenha o recurso Azure AD DS utilizando o cmdlet [Get-AzResource:][Get-AzResource]

> [!TIP]
> Se receber um erro utilizando o comando [Get-AzResource][Get-AzResource] que o recurso *Microsoft.AAD/DomainServices* não existe, [eleve o seu acesso para gerir todas as subscrições e grupos de gestão do Azure][global-admin].

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Em seguida, defina *As Definições de Segurança do Domínio* para configurar as seguintes opções de segurança:

1. Desativar o suporte NTLM v1.
2. Desative a sincronização das hashes de senha NTLM a partir do seu Anúncio no local.
3. Desativar o TLS v1.

> [!IMPORTANT]
> Os utilizadores e as contas de serviço não podem executar ligações simples LDAP se desativar a sincronização de hash de senha NTLM no domínio gerido pelo Azure AD DS. Se necessitar de executar ligações simples LDAP, não detete as *"SyncNtlmPasswords"="Desativado";* opção de configuração de segurança no seguinte comando.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

Por fim, aplique as definições de segurança definidas no domínio gerido pelo Azure AD DS utilizando o cmdlet [Set-AzResource.][Set-AzResource] Especifique o recurso Azure AD DS a partir do primeiro passo e as definições de segurança do passo anterior.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

Leva alguns momentos para as definições de segurança serem aplicadas ao domínio gerido pelo Azure AD DS.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o processo de sincronização, veja [como objetos e credenciais são sincronizados num domínio gerido por AD DS azure][synchronization].

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