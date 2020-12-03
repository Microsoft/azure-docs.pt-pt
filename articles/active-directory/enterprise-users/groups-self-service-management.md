---
title: Criar gestão de grupos de self-service - Azure Ative Directory / Microsoft Docs
description: Criar e gerir grupos de segurança ou grupos Microsoft 365 no Azure Ative Directory e solicitar membros do grupo de segurança ou microsoft 365
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6b2b8e3374c362f937aa5cfe106e8da9f9aa39f
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548006"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Criar gestão de grupos de self-service no Azure Ative Directory 

Pode permitir aos utilizadores criar e gerir os seus próprios grupos de segurança ou grupos Microsoft 365 no Azure Ative Directory (Azure AD). O proprietário do grupo pode aprovar ou negar pedidos de adesão, e pode delegar o controlo da adesão ao grupo. As funcionalidades de gestão do grupo self-service não estão disponíveis para grupos de segurança ou listas de distribuição habilitadas por correio.

## <a name="self-service-group-membership-defaults"></a>Incumprimentos de adesão ao grupo de autosserviço

Quando os grupos de segurança são criados no portal Azure ou utilizando o Azure AD PowerShell, apenas os proprietários do grupo podem atualizar a adesão. Grupos de segurança criados por self-service no [painel Access](https://account.activedirectory.windowsazure.com/r#/joinGroups) e todos os grupos Microsoft 365 estão disponíveis para se juntarem a todos os utilizadores, aprovados pelo proprietário ou aprovados automaticamente. No painel Access, pode alterar as opções de adesão quando criar o grupo.

Grupos criados em | Comportamento padrão do grupo de segurança | Microsoft 365 group default behavior
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](../enterprise-users/groups-settings-cmdlets.md) | Só os proprietários podem adicionar membros<br>Visível mas não disponível para participar no painel Access | Aberto para aderir a todos os utilizadores
[Portal do Azure](https://portal.azure.com) | Só os proprietários podem adicionar membros<br>Visível mas não disponível para participar no painel Access<br>O proprietário não é atribuído automaticamente na criação de grupo | Aberto para aderir a todos os utilizadores
[Painel de acesso](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Aberto para aderir a todos os utilizadores<br>As opções de adesão podem ser alteradas quando o grupo é criado | Aberto para aderir a todos os utilizadores<br>As opções de adesão podem ser alteradas quando o grupo é criado

## <a name="self-service-group-management-scenarios"></a>Cenários de gestão de grupos de self-service

* **Gestão delegada do grupo** Um exemplo é um administrador que está a gerir o acesso a uma aplicação SaaS que a empresa está a usar. A gestão destes direitos de acesso está a tornar-se complexa, por isso este administrador pede ao proprietário da empresa que crie um novo grupo. O administrador atribui acesso à aplicação ao novo grupo, e adiciona ao grupo todas as pessoas que já acedem à aplicação. O proprietário da empresa pode então adicionar mais utilizadores e estes são aprovisionados automaticamente para a aplicação. O proprietário da empresa não tem de aguardar que o administrador faça a gestão do acesso dos utilizadores. Se o administrador conceder a mesma permissão a um gestor num grupo empresarial diferente, essa pessoa também pode gerir o acesso aos seus próprios membros do grupo. Nem o empresário nem o gestor podem ver ou gerir os membros do grupo uns dos outros. O administrador ainda pode ver todos os utilizadores que têm acesso à aplicação e bloquear os direitos de acesso, se for necessário.
* **Gestão de grupos de self-service** Um exemplo deste cenário são dois utilizadores que ambos têm sites SharePoint Online que configuram de forma independente. Querem dar às equipas um do outro acesso aos seus sites. Para o conseguirem, podem criar um grupo no Azure AD e no SharePoint Online cada um deles seleciona esse grupo para fornecer acesso aos respetivos sites. Quando alguém quiser ter acesso, pode solicitá-lo no Painel de Acesso e, após a aprovação, obtém automaticamente acesso a ambos os sites do SharePoint Online. Posteriormente, um deles decide que todas as pessoas que acedem ao site devem também ter acesso a uma determinada aplicação SaaS. O administrador da aplicação SaaS pode adicionar direitos de acesso da aplicação ao site do SharePoint Online. A partir daí, quaisquer pedidos que ele aprove concedem acesso aos dois sites SharePoint Online e também a esta aplicação SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Disponibilizar um grupo para personalização pelo utilizador

1. Inicie sessão no [Centro de administradores do Azure AD](https://aad.portal.azure.com) com uma conta que seja administrador global do diretório.
1. Selecione **Grupos** e, em seguida, selecione **definições gerais.**
1. Os **Proprietários de Conjuntos podem gerir os pedidos de membros do grupo no Painel de Acesso** a **Sim**.
1. Definir **Restringir o acesso a grupos no Painel de Acesso** ao **Nº**.
1. Se definir **os Utilizadores podem criar grupos de segurança em portais Azure** ou **os utilizadores podem criar grupos Microsoft 365 em portais Azure** para

    - **Sim:** Todos os utilizadores da sua organização Azure AD estão autorizados a criar novos grupos de segurança e adicionar membros a estes grupos. Estes novos grupos apareceriam também no Painel de Acesso para todos os outros utilizadores. Se a definição de política no grupo permitir, outros utilizadores podem criar pedidos para se juntarem a estes grupos
    - **Não:** Os utilizadores não podem criar grupos e não podem alterar os grupos existentes para os quais são proprietários. No entanto, podem continuar gerir os membros desses grupos e aprovar pedidos de outros utilizadores para pertencer aos respetivos grupos.

Também pode utilizar **os Proprietários que podem atribuir membros como proprietários do grupo no portal Azure** para obter um maior controlo de acesso granular sobre a gestão do grupo de self-service para os seus utilizadores.

Quando os utilizadores podem criar grupos, todos os utilizadores da sua organização podem criar novos grupos e, em seguida, como proprietário padrão, adicionar membros a estes grupos. Não se pode especificar indivíduos que podem criar os seus próprios grupos. Pode especificar indivíduos apenas para tornar outro membro do grupo um proprietário do grupo.

> [!NOTE]
> É necessária uma licença Azure Ative Directory Premium (P1 ou P2) para que os utilizadores solicitem a adesão a um grupo de segurança ou ao grupo Microsoft 365 e que os proprietários aprovem ou neguem pedidos de adesão. Sem uma licença Azure Ative Directory Premium, os utilizadores ainda podem gerir os seus grupos no Painel de Acesso, mas não podem criar um grupo que exija a aprovação do proprietário no Painel de Acesso, e não podem solicitar a adesão a um grupo.

## <a name="next-steps"></a>Passos seguintes

Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

* [Gerir o acesso a recursos com grupos do Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Cmdlets do Azure Active Directory para configurar definições de grupo](../enterprise-users/groups-settings-cmdlets.md)
* [Gestão de Aplicações no Azure Active Directory](../manage-apps/what-is-application-management.md)
* [O que é o Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrar as identidades no local no Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
