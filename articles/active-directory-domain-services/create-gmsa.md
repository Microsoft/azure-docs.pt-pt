---
title: Contas de serviço geridas pelo grupo para serviços de domínio Azure AD Microsoft Docs
description: Saiba como criar uma conta de serviço gerida pelo grupo (gMSA) para utilização com domínios geridos por Azure Ative Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: f975d3e0e605b7c24b9fd31dc8fc78f0f37bb6b9
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619989"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-active-directory-domain-services"></a>Criar uma conta de serviço gerida pelo grupo (gMSA) em Azure Ative Directory Domain Services

As aplicações e serviços precisam muitas vezes de uma identidade para se autenticarem com outros recursos. Por exemplo, um serviço web pode precisar de autenticar com um serviço de base de dados. Se uma aplicação ou serviço tiver múltiplas instâncias, como uma fazenda de servidores web, criar e configurar manualmente as identidades para esses recursos torna-se demorado.

Em vez disso, uma conta de serviço gerida pelo grupo (gMSA) pode ser criada no domínio gerido pelo Azure Ative Directory Domain Services (Azure AD DS). O Windows OS gere automaticamente as credenciais para um gMSA, o que simplifica a gestão de grandes grupos de recursos.

Este artigo mostra-lhe como criar um gMSA num domínio gerido usando a Azure PowerShell.

## <a name="before-you-begin"></a>Before you begin

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, complete o tutorial para criar e configurar um domínio gerido pelos [Serviços de Domínio do Diretório Ativo Azure][create-azure-ad-ds-instance].
* Um VM de gestão de servidores do Windows que se junta ao domínio gerido Azure AD DS.
    * Se necessário, complete o tutorial para [criar um VM de gestão.][tutorial-create-management-vm]

## <a name="managed-service-accounts-overview"></a>Visão geral das contas de serviço geridas

Uma conta de serviço gerida autónoma (sMSA) é uma conta de domínio cuja palavra-passe é gerida automaticamente. Esta abordagem simplifica a gestão do nome principal do serviço (SPN) e permite a gestão delegada a outros administradores. Não é necessário criar e rodar manualmente credenciais para a conta.

Uma conta de serviço gerida pelo grupo (gMSA) fornece a mesma simplificação de gestão, mas para vários servidores no domínio. Um gMSA permite que todas as instâncias de um serviço hospedado numa fazenda de servidores utilizem o mesmo principal serviço para protocolos de autenticação mútua funcionarem. Quando um gMSA é usado como principal de serviço, o sistema operativo Windows gere novamente a palavra-passe da conta em vez de depender do administrador.

Para obter mais informações, consulte [as contas de serviço geridas pelo grupo (gMSA).][gmsa-overview]

## <a name="using-service-accounts-in-azure-ad-ds"></a>Utilização de contas de serviço em Azure AD DS

Como os domínios geridos são bloqueados e geridos pela Microsoft, existem algumas considerações ao utilizar contas de serviço:

* Crie contas de serviço em unidades organizacionais personalizadas (OU) no domínio gerido.
    * Não é possível criar uma conta de serviço nos *Utilizadores AADDC* incorporados ou em OUs *de Computadores AADDC.*
    * Em vez disso, [crie um OU personalizado][create-custom-ou] no domínio gerido e, em seguida, crie contas de serviço nesse ou personalizado.
* A chave raiz dos Serviços de Distribuição chave (KDS) é pré-criada.
    * A chave raiz KDS é usada para gerar e recuperar senhas para gMSAs. Em Azure AD DS, a raiz KDS é criada para si.
    * Não tem privilégios para criar outra, ou ver a chave de raiz KDS padrão.

## <a name="create-a-gmsa"></a>Criar um gMSA

Em primeiro lugar, crie um OU personalizado utilizando o [cmdlet New-ADOrganizationalUnit.][New-AdOrganizationalUnit] Para obter mais informações sobre a criação e gestão de OUs personalizadas, consulte [OUs personalizados em Azure AD DS][create-custom-ou].

> [!TIP]
> Para completar estes passos para criar um gMSA, [utilize o seu VM de gestão.][tutorial-create-management-vm] Esta VM de gestão já deve ter os cmdlets AD PowerShell necessários e ligação ao domínio gerido.

O exemplo a seguir cria um OU personalizado chamado *myNewOU* no domínio gerido denominado *aaddscontoso.com*. Use o seu próprio nome de domínio ou gerido:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

Agora crie um gMSA utilizando o [cmdlet New-ADServiceAccount.][New-ADServiceAccount] São definidos os seguintes parâmetros de exemplo:

* **-O nome** está definido para *WebFarmSvc*
* **-O** parâmetro do caminho especifica o ou personalizado para o gMSA criado no passo anterior.
* As entradas de DNS e os nomes principais do serviço estão definidos para *WebFarmSvc.aaddscontoso.com*
* Os principais em *AADDSCONTOSO-SERVER$* são autorizados a recuperar a palavra-passe e a usar a identidade.

Especifique os seus próprios nomes e nomes de domínio.

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.aaddscontoso.com `
    -Path "OU=MYNEWOU,DC=aaddscontoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.aaddscontoso.com/aaddscontoso.com, `
        http/WebFarmSvc.aaddscontoso.com/aaddscontoso, `
        http/WebFarmSvc/aaddscontoso.com, `
        http/WebFarmSvc/aaddscontoso `
    -PrincipalsAllowedToRetrieveManagedPassword AADDSCONTOSO-SERVER$
```

As aplicações e serviços podem agora ser configurados para utilizar o gMSA conforme necessário.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre os gMSAs, consulte [começar com contas de serviço geridas pelo grupo.][gmsa-start]

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
