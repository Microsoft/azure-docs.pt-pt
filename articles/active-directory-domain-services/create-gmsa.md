---
title: Contas de serviço geridas pelo grupo para os Serviços de Domínio Da Azure AD  Microsoft Docs
description: Saiba como criar uma conta de serviço gerida por grupo (gMSA) para utilização com domínios geridos pelo Azure Ative Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 58749e4518f6fa73c8641ce38483c101576047aa
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77614085"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-ad-domain-services"></a>Criar uma conta de serviço gerida por grupo (gMSA) nos Serviços de Domínio Azure AD

As aplicações e serviços precisam muitas vezes de uma identidade para se autenticarem com outros recursos. Por exemplo, um serviço web pode ter de autenticar com um serviço de base de dados. Se uma aplicação ou serviço tiver múltiplas instâncias, como uma exploração de servidores web, criar manualmente e configurar as identidades para esses recursos torna-se demorado.

Em vez disso, uma conta de serviço gerida por grupo (gMSA) pode ser criada no domínio gerido pelo Azure Ative Directory Domain Services (Azure AD DS). O Windows OS gere automaticamente as credenciais para um gMSA, o que simplifica a gestão de grandes grupos de recursos.

Este artigo mostra-lhe como criar um gMSA num domínio gerido por Azure AD DS utilizando o Azure PowerShell.

## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
    * Se necessário, complete o tutorial para criar e configurar uma instância de Serviços de [Domínio de Diretório Ativo Azure.][create-azure-ad-ds-instance]
* Um VM de gestão do Servidor Windows que se junta ao domínio gerido pelo Azure AD DS.
    * Se necessário, complete o tutorial para criar um VM de [gestão.][tutorial-create-management-vm]

## <a name="managed-service-accounts-overview"></a>Visão geral das contas de serviço geridas

Uma conta de serviço gerida autónoma (sMSA) é uma conta de domínio cuja palavra-passe é gerida automaticamente. Esta abordagem simplifica a gestão do nome principal do serviço (SPN) e permite a gestão delegada a outros administradores. Não é necessário criar manualmente e rodar credenciais para a conta.

Uma conta de serviço gerida por grupo (gMSA) fornece a mesma simplificação de gestão, mas para vários servidores no domínio. Um gMSA permite que todos os casos de um serviço hospedado numa quinta de servidores utilizem o mesmo principal de serviço para protocolos de autenticação mútua funcionarem. Quando um gMSA é usado como diretor de serviço, o sistema operativo Windows volta a gerir a palavra-passe da conta em vez de confiar no administrador.

Para mais informações, consulte a visão geral das contas de [serviço geridas pelo grupo (gMSA).][gmsa-overview]

## <a name="using-service-accounts-in-azure-ad-ds"></a>Utilização de contas de serviço em Azure AD DS

Como os domínios geridos pelo Azure AD DS são bloqueados e geridos pela Microsoft, existem algumas considerações ao utilizar contas de serviço:

* Crie contas de serviço em unidades organizacionais personalizadas (OU) no domínio gerido.
    * Não é possível criar uma conta de serviço nos *Utilizadores AADDC* incorporados ou *em Computadores AADDC* OUs.
    * Em vez disso, [crie um OU personalizado][create-custom-ou] no domínio gerido pelo Azure AD DS e, em seguida, crie contas de serviço nesse OU personalizado.
* A chave de raiz dos Serviços de Distribuição (KDS) está pré-criada.
    * A chave de raiz KDS é usada para gerar e recuperar senhas para gMSAs. Em Azure AD DS, a raiz KDS é criada para si.
    * Você não tem privilégios para criar outra, ou ver a chave de raiz kDS padrão.

## <a name="create-a-gmsa"></a>Criar um gMSA

Em primeiro lugar, crie um OU personalizado utilizando o cmdlet [New-ADOrganizationalUnit.][New-AdOrganizationalUnit] Para obter mais informações sobre a criação e gestão de OUs personalizados, consulte [As OUs personalizadas em DS AD Azure][create-custom-ou].

> [!TIP]
> Para completar estes passos para criar um gMSA, [utilize o seu VM][tutorial-create-management-vm]de gestão . Este VM de gestão já deve ter os cmdlets AD PowerShell necessários e a ligação ao domínio gerido.

O exemplo seguinte cria um OU personalizado chamado *myNewOU* no domínio gerido azure AD DS chamado *aaddscontoso.com*. Use o seu próprio nome de domínio ou e gerido:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

Agora crie um gMSA utilizando o cmdlet [New-ADServiceAccount.][New-ADServiceAccount] São definidos os seguintes parâmetros de exemplo:

* **-O nome** está definido para *WebFarmSvc*
* **-O** parâmetro path especifica o OU personalizado para o gMSA criado no passo anterior.
* As entradas dNS e os principais nomes do serviço estão definidos para *WebFarmSvc.aaddscontoso.com*
* Os principais da *AADDSCONTOSO-SERVER$* podem recuperar a palavra-passe, utilizar a identidade.

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

Para obter mais informações sobre gMSAs, consulte [Iniciar-se com contas de serviço geridas][gmsa-start]pelo grupo .

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
