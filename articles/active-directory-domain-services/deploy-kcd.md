---
title: Kerberos constrangiu delegação para a Azure AD Domain Services | Microsoft Docs
description: Saiba como permitir a delegação restrita kerberos (KCD) baseada em recursos num domínio gerido por Serviços de Domínio do Diretório Ativo Azure.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 138b90a33ff1dbc4b014f17fa0098112e1da66e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619781"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Configure Kerberos delegação restrita (KCD) em Azure Ative Directory Domain Services

À medida que executam as aplicações, pode haver uma necessidade para que essas aplicações acedam a recursos no contexto de um utilizador diferente. Os Serviços de Domínio do Diretório Ativo (DS DS) suportam um mecanismo chamado *delegação Kerberos* que permite este caso de utilização. A delegação *restrita* kerberos (KCD) baseia-se então neste mecanismo para definir recursos específicos que podem ser acedidos no contexto do utilizador.

Os domínios geridos Azure Ative Directory Domain (Azure AD DS) são mais bloqueados do que os ambientes tradicionais de DS AD no local, por isso utilize um KCD mais seguro *baseado em recursos.*

Este artigo mostra-lhe como configurar kerberos baseados em recursos delegação restrita em um domínio gerido AZure AD DS.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo, precisa dos seguintes recursos:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, [crie e configuure um domínio gerido por Azure Ative Directory Domain Services][create-azure-ad-ds-instance].
* Um VM de gestão de servidores do Windows que se junta ao domínio gerido Azure AD DS.
    * Se necessário, complete o tutorial para [criar um VM do Windows Server e junte-o a um domínio gerido][create-join-windows-vm] e, em seguida, [instale as ferramentas de gestão de DS AD][tutorial-create-management-vm].
* Uma conta de utilizador que é membro do grupo de administradores da *Ad DC Azure* no seu inquilino AZure AD.

## <a name="kerberos-constrained-delegation-overview"></a>Kerberos constrangido visão geral da delegação

A delegação kerberos permite que uma conta personiem outra conta para aceder aos recursos. Por exemplo, uma aplicação web que acede a um componente web de back-end pode fazer-se passar por uma conta de utilizador diferente quando faz a ligação back-end. A delegação kerberos é insegura, uma vez que não limita os recursos a que a conta de imitação pode aceder.

A delegação *restrita* kerberos (KCD) restringe os serviços ou recursos que um servidor ou aplicação especificado pode ligar ao personificar outra identidade. O KCD tradicional requer privilégios de administrador de domínio para configurar uma conta de domínio para um serviço, e restringe a conta a funcionar num único domínio.

O KCD tradicional também tem alguns problemas. Por exemplo, em sistemas operativos anteriores, o administrador de serviço não tinha forma útil de saber que serviços frontais delegados aos serviços de recursos que possuíam. Qualquer serviço frontal que pudesse delegar num serviço de recursos era um potencial ponto de ataque. Se um servidor que hospedava um serviço frontal configurado para delegar serviços de recursos fosse comprometido, os serviços de recursos também poderiam ser comprometidos.

Num domínio gerido, não tem privilégios de administrador de domínio. Como resultado, o KCD baseado em contas tradicionais não pode ser configurado num domínio gerido. Em vez disso, o KCD baseado em recursos pode ser utilizado, o que também é mais seguro.

### <a name="resource-based-kcd"></a>KCD baseado em recursos

O Windows Server 2012 e mais tarde dá aos administradores de serviço a capacidade de configurar uma delegação restrita para o seu serviço. Este modelo é conhecido como KCD baseado em recursos. Com esta abordagem, o administrador de serviço de back-end pode permitir ou negar serviços frontais específicos da utilização do KCD.

O KCD baseado em recursos está configurado utilizando o PowerShell. Utiliza os cmdlets [Set-ADComputer][Set-ADComputer] ou [Set-ADUser,][Set-ADUser] dependendo se a conta de imitação é uma conta de computador ou uma conta de utilizador/conta de serviço.

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>Configure o KCD baseado em recursos para uma conta de computador

Neste cenário, vamos supor que tem uma aplicação web que funciona no computador chamado *contoso-webapp.aaddscontoso.com*.

A aplicação web precisa de aceder a uma API web que funciona no computador chamado *contoso-api.aaddscontoso.com* no contexto dos utilizadores de domínio.

Complete os seguintes passos para configurar este cenário:

1. [Crie um OU personalizado.](create-ou.md) Pode delegar permissões para gerir este OU personalizado para os utilizadores dentro do domínio gerido.
1. [Junte-se às máquinas virtuais][create-join-windows-vm], tanto a que executa a aplicação web, como a que executa a API web, para o domínio gerido. Crie estas contas de computador no OU personalizado a partir do passo anterior.

    > [!NOTE]
    > As contas do computador para a aplicação web e a API web devem estar em um OU personalizado onde você tem permissões para configurar KCD baseado em recursos. Não é possível configurar o KCD baseado em recursos para uma conta de computador no contentor *AAD DC Computers* incorporado.

1. Finalmente, configurar kCD baseado em recursos utilizando o [cmdlet Set-ADComputer][Set-ADComputer] PowerShell.

    A partir da sua gestão de domínio VM e inseriu-se como conta de utilizador que é membro do grupo de *administradores AD DC Azure,* executar os seguintes cmdlets. Forneça os seus próprios nomes de computador conforme necessário:
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aaddscontoso.com
    Set-ADComputer contoso-api.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>Configure kCD baseado em recursos para uma conta de utilizador

Neste cenário, vamos supor que tem uma aplicação web que funciona como uma conta de serviço chamada *appsvc*. A aplicação web precisa de aceder a uma API web que funciona como uma conta de serviço chamada *backendsvc* no contexto dos utilizadores de domínio. Complete os seguintes passos para configurar este cenário:

1. [Crie um OU personalizado.](create-ou.md) Pode delegar permissões para gerir este OU personalizado para os utilizadores dentro do domínio gerido.
1. [Junte-se ao domínio as máquinas virtuais][create-join-windows-vm] que executam a API/recurso web backend para o domínio gerido. Crie a sua conta de computador dentro da U. personalizada.
1. Crie a conta de serviço (por exemplo, *appsvc)* usada para executar a aplicação web dentro da OU personalizada.

    > [!NOTE]
    > Mais uma vez, a conta do computador para a web API VM, e a conta de serviço para a aplicação web, deve estar em um OU personalizado onde você tem permissões para configurar KCD baseado em recursos. Não é possível configurar o KCD baseado em recursos para contas nos recipientes de *utilizadores AAD DC* incorporados ou *AAD DC.* Isto também significa que não pode utilizar as contas de utilizador sincronizadas a partir do Azure AD para configurar o KCD baseado em recursos. Deve criar e utilizar contas de serviço especificamente criadas em Azure AD DS.

1. Finalmente, configurar o KCD baseado em recursos utilizando o [cmdlet Set-ADUser][Set-ADUser] PowerShell.

    A partir da sua gestão de domínio VM e inseriu-se como conta de utilizador que é membro do grupo de *administradores AD DC Azure,* executar os seguintes cmdlets. Forneça os seus próprios nomes de serviço conforme necessário:

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como funciona a delegação nos Serviços de Domínio do Diretório Ativo, consulte [a Visão Geral da Delegação Restrita kerberos.][kcd-technet]

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)