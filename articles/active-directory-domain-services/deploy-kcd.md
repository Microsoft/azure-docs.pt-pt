---
title: Kerberos constrangido delegação para serviços de domínio azure ad [ Microsoft Docs
description: Saiba como permitir a delegação limitada kerberos (KCD) num domínio gerido pelo Azure Ative Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 07aa9ade25d1d986833b6da2f3907d07b752b662
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80655432"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Configure Kerberos constrangido delegação (KCD) em Serviços de Domínio de Diretório Ativo Azure

À medida que executa aplicações, pode haver necessidade de essas aplicações acederem a recursos no contexto de um utilizador diferente. Ative Directory Domain Services (AD DS) apoia um mecanismo chamado *delegação Kerberos* que permite este caso de utilização. A delegação *restrita* kerberos (KCD) baseia-se então neste mecanismo para definir recursos específicos que podem ser acedidos no contexto do utilizador. Os domínios geridos pelo Azure Ative Directory (Azure AD DS) são mais seguros do que os ambientes Tradicionais aD DS no local, por isso use um KCD mais seguro *baseado em recursos.*

Este artigo mostra-lhe como configurar a delegação limitada kerberos baseada em recursos num domínio gerido pela AD DS azure.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo, precisa dos seguintes recursos:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
    * Se necessário, crie e configure uma instância de Serviços de [Domínio de Diretório Ativo Azure][create-azure-ad-ds-instance].
* Um VM de gestão do Servidor Windows que se junta ao domínio gerido pelo Azure AD DS.
    * Se necessário, complete o tutorial para criar um VM do [Windows Server e junte-o a um domínio gerido][create-join-windows-vm] e, em seguida, instale as ferramentas de [gestão AD DS][tutorial-create-management-vm].
* Uma conta de utilizador que é membro do grupo de administradores da *Azure AD DC* no seu inquilino Azure AD.

## <a name="kerberos-constrained-delegation-overview"></a>Kerberos limitou a visão geral da delegação

A delegação da Kerberos permite que uma conta se personique outra conta para aceder aos recursos. Por exemplo, uma aplicação web que acede a um componente web de back-end pode personificar-se como uma conta de utilizador diferente quando faz a ligação de back-end. A delegação de Kerberos é insegura, uma vez que não limita os recursos a que a conta personificada pode aceder.

A delegação restrita de Kerberos (KCD) restringe os serviços ou recursos que um servidor ou aplicação especificado pode ligar quando se faz passar por outra identidade. O KCD tradicional requer privilégios de administrador de domínio para configurar uma conta de domínio para um serviço, e restringe a conta a ser executada num único domínio.

O KCD tradicional também tem alguns problemas. Por exemplo, em sistemas operativos anteriores, o administrador de serviços não tinha forma útil de saber quais os serviços front-end delegados aos serviços de recursos que possuíam. Qualquer serviço frontal que pudesse delegar num serviço de recursos era um potencial ponto de ataque. Se um servidor que acolhesse um serviço frontal configurado para delegar nos serviços de recursos fosse comprometido, os serviços de recursos também poderiam ser comprometidos.

Num domínio gerido pelo Azure AD DS, não tem privilégios de administrador de domínio. Como resultado, o KCD tradicional baseado em contas não pode ser configurado num Azure AD DS um domínio gerido. Em vez disso, o KCD baseado em recursos pode ser utilizado, o que também é mais seguro.

### <a name="resource-based-kcd"></a>KCD baseado em recursos

O Windows Server 2012 e, mais tarde, dá aos administradores de serviço a capacidade de configurar a delegação restrita para o seu serviço. Este modelo é conhecido como KCD baseado em recursos. Com esta abordagem, o administrador de serviço de back-end pode permitir ou negar serviços frontais específicos da utilização do KCD.

O KCD baseado em recursos é configurado usando powerShell. Utiliza os cmdlets [Set-ADComputer][Set-ADComputer] ou [Set-ADUser,][Set-ADUser] dependendo se a conta de representação é uma conta de computador ou uma conta de utilizador/conta de serviço.

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>Configure o KCD baseado em recursos para uma conta de computador

Neste cenário, vamos assumir que tem uma aplicação web que funciona no computador chamado *contoso-webapp.aaddscontoso.com*. A aplicação web precisa de aceder a uma API web que funciona no computador chamado *contoso-api.aaddscontoso.com* no contexto dos utilizadores de domínio. Complete os seguintes passos para configurar este cenário:

1. [Crie um OU personalizado.](create-ou.md) Pode delegar permissões para gerir este OU personalizado aos utilizadores dentro do domínio gerido pelo Azure AD DS.
1. [Junte-se ao domínio das máquinas virtuais][create-join-windows-vm], tanto a que executa a aplicação web, como a que executa a Web API, para o domínio gerido pelo Azure AD DS. Crie estas contas de computador na UA personalizada a partir do passo anterior.

    > [!NOTE]
    > As contas do computador para a aplicação web e a Web API devem estar num OU personalizado onde você tem permissões para configurar o KCD baseado em recursos. Não é possível configurar o KCD baseado em recursos para uma conta de computador no recipiente incorporado da *AAD DC Computers.*

1. Por fim, configure o KCD baseado em recursos utilizando o cmdlet [Set-ADComputer][Set-ADComputer] PowerShell. A partir da sua VM de gestão filiada em domínio e registrado como conta de utilizador que é membro do grupo de *administradores da AD DC Azure,* executa os seguintes cmdlets. Forneça os seus próprios nomes de computador conforme necessário:
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aaddscontoso.com
    Set-ADComputer contoso-api.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>Configure o KCD baseado em recursos para uma conta de utilizador

Neste cenário, vamos assumir que tem uma aplicação web que funciona como uma conta de serviço chamada *appsvc*. A aplicação web precisa de aceder a uma API web que funciona como uma conta de serviço chamada *backendsvc* no contexto dos utilizadores de domínio. Complete os seguintes passos para configurar este cenário:

1. [Crie um OU personalizado.](create-ou.md) Pode delegar permissões para gerir este OU personalizado aos utilizadores dentro do domínio gerido pelo Azure AD DS.
1. [Junte-se ao domínio das máquinas virtuais][create-join-windows-vm] que executam a Web API/recurso backend para o domínio gerido pelo Azure AD DS. Crie a sua conta de computador dentro da Ou personalizada.
1. Criar a conta de serviço (por exemplo, 'appsvc') usada para executar a aplicação web dentro da OU personalizada.

    > [!NOTE]
    > Mais uma vez, a conta do computador para a Web API VM, e a conta de serviço para a aplicação web, deve estar em um OU personalizado onde você tem permissões para configurar kcd baseado em recursos. Não é possível configurar o KCD baseado em recursos para contas nos recipientes *AAD DC Computers* ou *AAD DC Users* incorporados. Isto também significa que não pode utilizar contas de utilizador sincronizadas a partir de Azure AD para configurar o KCD baseado em recursos. Deve criar e utilizar contas de serviço especificamente criadas em DS AD Azure.

1. Por fim, configure o KCD baseado em recursos utilizando o cmdlet [Set-ADUser][Set-ADUser] PowerShell. A partir da sua VM de gestão filiada em domínio e registrado como conta de utilizador que é membro do grupo de *administradores da AD DC Azure,* executa os seguintes cmdlets. Forneça os seus próprios nomes de serviço conforme necessário:

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre como a delegação trabalha em Serviços de Domínio de Diretório Ativo, consulte a [visão geral da delegação restrita kerberos.][kcd-technet]

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: https://technet.microsoft.com/library/jj553400.aspx
