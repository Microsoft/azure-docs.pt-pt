---
title: Delegação restrita de Kerberos para Azure AD Domain Services | Microsoft Docs
description: Saiba como habilitar a KCD (delegação restrita de Kerberos com base em recursos) em um domínio gerenciado Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: iainfou
ms.openlocfilehash: 89bc690e5a8c8d24d7732dd4e12f70a9f1f368af
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842652"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Configurar a KCD (delegação restrita de Kerberos) no Azure Active Directory Domain Services

À medida que você executa aplicativos, pode haver a necessidade desses aplicativos acessarem recursos no contexto de um usuário diferente. Active Directory Domain Services (AD DS) dá suporte a um mecanismo chamado *delegação de Kerberos* que habilita esse caso de uso. A delegação *restrita* de Kerberos (KCD) é criada nesse mecanismo para definir recursos específicos que podem ser acessados no contexto do usuário. Os domínios gerenciados do Azure Active Directory Domain Services (AD DS do Azure) são bloqueados com mais segurança em ambientes de AD DS locais tradicionais, portanto, use um KCD *baseado em recursos* mais seguro.

Este artigo mostra como configurar a delegação restrita de Kerberos de basd de recursos em um domínio gerenciado AD DS do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa dos seguintes recursos:

* Uma subscrição ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário Azure Active Directory associado à sua assinatura, seja sincronizado com um diretório local ou um diretório somente em nuvem.
    * Se necessário, [crie um locatário Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um Azure Active Directory Domain Services domínio gerenciado habilitado e configurado em seu locatário do Azure AD.
    * Se necessário, [crie e configure uma instância de Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma VM de gerenciamento do Windows Server que é unida ao domínio gerenciado AD DS do Azure.
    * Se necessário, conclua o tutorial para [criar uma VM do Windows Server e ingresse-a em um domínio gerenciado][create-join-windows-vm] e, em seguida, [Instale as ferramentas de gerenciamento de AD DS][tutorial-create-management-vm].
* Uma conta de usuário que é membro do grupo de *Administradores de DC do Azure ad* em seu locatário do Azure AD.

## <a name="kerberos-constrained-delegation-overview"></a>Visão geral da delegação restrita de Kerberos

A delegação de Kerberos permite que uma conta represente outra conta para acessar recursos. Por exemplo, um aplicativo Web que acessa um componente da Web de back-end pode se representar como uma conta de usuário diferente quando faz a conexão de back-end. A delegação de Kerberos não é segura, pois não limita quais recursos a conta representando pode acessar.

A delegação restrita de Kerberos (KCD) restringe os serviços ou os recursos que um servidor ou aplicativo especificado pode conectar ao representar outra identidade. O KCD tradicional exige privilégios de administrador de domínio para configurar uma conta de domínio para um serviço e restringe a conta a ser executada em um único domínio. O KCD tradicional também tem alguns problemas. Por exemplo, em sistemas operacionais anteriores, o administrador de serviços não tinha uma maneira útil de saber quais serviços de front-end eram delegados aos serviços de recursos que eles possuíam. Qualquer serviço de front-end que pode delegar a um serviço de recurso era um possível ponto de ataque. Se um servidor que hospedasse um serviço de front-end configurado para delegar aos serviços de recursos tiver sido comprometido, os serviços de recursos também poderão ser comprometidos.

Em um domínio gerenciado do Azure AD DS, você não tem privilégios de administrador de domínio. Como resultado, as KCD tradicionais baseadas em contas não podem ser configuradas em um AD DS do Azure em um domínio gerenciado. Em vez disso, o KCD baseado em recursos pode ser usado, o que também é mais seguro.

### <a name="resource-based-kcd"></a>KCD com base em recursos

O Windows Server 2012 e posterior concede aos administradores de serviço a capacidade de configurar a delegação restrita para seu serviço. Esse modelo é conhecido como KCD baseada em recursos. Com essa abordagem, o administrador de serviços de back-end pode permitir ou negar serviços de front-end específicos do uso do KCD.

O KCD baseado em recursos é configurado usando o PowerShell. Você usa os cmdlets [set-ADComputer][Set-ADComputer] ou [set-ADUser][Set-ADUser] , dependendo se a conta de representação é uma conta de computador ou uma conta de usuário/serviço.

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>Configurar KCD com base em recursos para uma conta de computador

Neste cenário, vamos supor que você tenha um aplicativo Web que é executado no computador chamado *contoso-webapp.contoso.com*. O aplicativo Web precisa acessar uma API da Web que é executada no computador chamado *contoso-API.contoso.com* no contexto de usuários do domínio. Conclua as seguintes etapas para configurar este cenário:

1. [Crie uma UO personalizada](create-ou.md). Você pode delegar permissões para gerenciar essa UO personalizada para usuários dentro do domínio gerenciado AD DS do Azure.
1. [Domínio – ingresse nas máquinas virtuais][create-join-windows-vm], ambas que executam o aplicativo Web e a que executa a API Web, para o domínio gerenciado AD DS do Azure. Crie essas contas de computador na UO personalizada da etapa anterior.

    > [!NOTE]
    > As contas de computador para o aplicativo Web e a API Web devem estar em uma UO personalizada em que você tenha permissões para configurar KCD com base em recursos. Você não pode configurar o KCD baseado em recursos para uma conta de computador no contêiner de *computadores DC do AAD* interno.

1. Por fim, configure KCD com base em recursos usando o cmdlet do PowerShell [set-ADComputer][Set-ADComputer] . Em sua VM de gerenciamento ingressado no domínio e conectada como uma conta de usuário que seja membro do grupo de *Administradores de DC do Azure ad* , execute os cmdlets a seguir. Forneça seus próprios nomes de computador, conforme necessário:
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.contoso.com
    Set-ADComputer contoso-api.contoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>Configurar KCD com base em recursos para uma conta de usuário

Neste cenário, vamos supor que você tenha um aplicativo Web que seja executado como uma conta de serviço chamada *appsvc*. O aplicativo Web precisa acessar uma API Web que é executada como uma conta de serviço chamada *backendsvc* no contexto de usuários do domínio. Conclua as seguintes etapas para configurar este cenário:

1. [Crie uma UO personalizada](create-ou.md). Você pode delegar permissões para gerenciar essa UO personalizada para usuários dentro do domínio gerenciado AD DS do Azure.
1. [Domínio – ingresse nas máquinas virtuais][create-join-windows-vm] que executam a API/recurso de back-end da Web para o domínio gerenciado AD DS do Azure. Crie sua conta de computador na UO personalizada.
1. Crie a conta de serviço (por exemplo, ' appsvc ') usada para executar o aplicativo Web dentro da UO personalizada.

    > [!NOTE]
    > Novamente, a conta de computador para a VM da API Web e a conta de serviço do aplicativo Web devem estar em uma UO personalizada em que você tenha permissões para configurar o KCD baseado em recursos. Você não pode configurar KCD com base em recursos para contas nos contêineres de *computadores DC do AAD* internos ou *usuários do AAD DC* . Isso também significa que você não pode usar contas de usuário sincronizadas do Azure AD para configurar o KCD baseado em recursos. Você deve criar e usar contas de serviço criadas especificamente no Azure AD DS.

1. Por fim, configure KCD com base em recursos usando o cmdlet do PowerShell [set-ADUser][Set-ADUser] . Em sua VM de gerenciamento ingressado no domínio e conectada como uma conta de usuário que seja membro do grupo de *Administradores de DC do Azure ad* , execute os cmdlets a seguir. Forneça seus próprios nomes de serviço, conforme necessário:

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre como a delegação funciona em Active Directory Domain Services, consulte [visão geral da delegação restrita de Kerberos][kcd-technet].

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
