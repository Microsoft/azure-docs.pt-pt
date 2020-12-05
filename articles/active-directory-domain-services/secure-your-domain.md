---
title: Serviços de Domínio Azure Seguro / Microsoft Docs
description: Saiba como desativar cifras fracas, protocolos antigos e sincronização de hash de palavra-passe NTLM para um domínio gerido por Serviços de Domínio do Diretório Ativo Azure.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: a89c898e150facc9860d86e18a7acc42f5e0f441
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618863"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-active-directory-domain-services-managed-domain"></a>Desative cifras fracas e sincronização de hash de palavra-passe para garantir um domínio gerido por Serviços de Domínio do Diretório Ativo Azure

Por predefinição, os Serviços de Domínio do Diretório Ativo Azure (Azure AD DS) permitem a utilização de cifras como NTLM v1 e TLS v1. Estas cifras podem ser necessárias para algumas aplicações antigas, mas são consideradas fracas e podem ser desativadas se não precisar delas. Se tiver conectividade híbrida no local utilizando o Azure AD Connect, também pode desativar a sincronização dos hashes de palavra-passe NTLM.

Este artigo mostra-lhe como desativar cifras de NTLM v1 e TLS v1 e desativar a sincronização de hash de palavra-passe NTLM.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo, precisa dos seguintes recursos:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, [crie e configuure um domínio gerido por Azure Ative Directory Domain Services][create-azure-ad-ds-instance].
* Instalar e configurar o Azure PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo Azure PowerShell e ligue-se à sua assinatura Azure](/powershell/azure/install-az-ps).
    * Certifique-se de que faz sedução na sua assinatura Azure utilizando o [cmdlet Connect-AzAccount.][Connect-AzAccount]
* Instale e configuure Azure AD PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo Azure AD PowerShell e ligar-se ao Azure AD](/powershell/azure/active-directory/install-adv2).
    * Certifique-se de que faz sedundo ao seu inquilino AD Azure usando o cmdlet [Connect-AzureAD.][Connect-AzureAD]

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Desative cifras fracas e sincronização de hash de palavra-passe NTLM

Para desativar as fracas suítes de cifra e a sincronização de haxixe credencial NTLM, inscreva-se na sua conta Azure e, em seguida, obtenha o recurso AZure AD DS utilizando o cmdlet [Get-AzResource:][Get-AzResource]

> [!TIP]
> Se receber um erro utilizando o comando [Get-AzResource][Get-AzResource] de que o recurso *Microsoft.AAD/DomainServices* não existe, [eleve o seu acesso para gerir todas as subscrições e grupos de gestão do Azure.][global-admin]

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Em seguida, defina *o DomainSecuritySettings* para configurar as seguintes opções de segurança:

1. Desative o suporte NTLM v1.
2. Desative a sincronização dos hashes de palavra-passe NTLM a partir do seu AD no local.
3. Desative o TLS v1.

> [!IMPORTANT]
> Os utilizadores e contas de serviço não podem executar ligações simples LDAP se desativar a sincronização de hash de palavra-passe NTLM no domínio gerido por Azure AD DS. Se precisar de executar ligações simples LDAP, não desaperte as *"SyncNtlmPasswords"="Desativada";* opção de configuração de segurança no seguinte comando.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

Por fim, aplique as definições de segurança definidas no domínio gerido utilizando o [cmdlet Set-AzResource.][Set-AzResource] Especifique o recurso Azure AD DS desde o primeiro passo e as definições de segurança do passo anterior.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

Leva alguns momentos para que as definições de segurança sejam aplicadas ao domínio gerido.

> [!IMPORTANT]
> Depois de desativar o NTLM, efetue uma sincronização completa de hash de palavra-passe no Azure AD Connect para remover todos os hashes de palavra-passe do domínio gerido. Se desativar o NTLM mas não forçar uma sincronização de hash de palavra-passe, a palavra-passe NTLM só é removida na próxima alteração de palavra-passe. Este comportamento poderia permitir que um utilizador continuasse a iniciar sação se tivesse credenciais em cache num sistema onde o NTLM é utilizado como método de autenticação.
>
> Uma vez que o hash de senha NTLM é diferente do hash da palavra-passe Kerberos, o retorno para a NTLM não funcionará. As credenciais em cache também já não funcionam se o VM tiver conectividade com o controlador de domínio gerido.  

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o processo de sincronização, consulte [como os objetos e credenciais são sincronizados num domínio gerido][synchronization].

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
