---
title: Configurar o gerenciamento de grupo de autoatendimento-Azure Active Directory | Microsoft Docs
description: Criar e gerir grupos de segurança ou grupos do Office 365 no Azure Active Directory e pedir adesões ao grupo de segurança ou ao grupo do Office 365
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b52604d4ad20fed83c4649f046722ed45e766c4
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097702"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Configurar o gerenciamento de grupo de autoatendimento no Azure Active Directory 

Você pode permitir que os usuários criem e gerenciem seus próprios grupos de segurança ou grupos do Office 365 no Azure Active Directory (AD do Azure). O proprietário do grupo pode aprovar ou negar solicitações de associação e pode delegar o controle de associação de grupo. Os recursos de gerenciamento de grupo de autoatendimento não estão disponíveis para grupos de segurança ou listas de distribuição habilitadas para email.

## <a name="self-service-group-membership-defaults"></a>Padrões de associação de grupo de autoatendimento

Quando grupos de segurança são criados no portal do Azure ou usando o PowerShell do Azure AD, somente os proprietários do grupo podem atualizar a associação. Grupos de segurança criados no [painel de acesso](https://account.activedirectory.windowsazure.com/r#/joinGroups) e todos os grupos do Office 365 estão disponíveis para ingressar para todos os usuários, seja aprovado pelo proprietário ou aprovado automaticamente. No painel de acesso, você pode alterar as opções de associação ao criar o grupo.

Grupos criados em | Comportamento padrão do grupo de segurança | Comportamento padrão do grupo do Office 365
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | Somente proprietários podem adicionar membros<br>Visível, mas não disponível para junção no painel de acesso | Abrir para ingressar para todos os usuários
[Azure portal](https://portal.azure.com) | Somente proprietários podem adicionar membros<br>Visível, mas não disponível para junção no painel de acesso<br>O proprietário não é atribuído automaticamente na criação do grupo | Abrir para ingressar para todos os usuários
[Painel de acesso](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Abrir para ingressar para todos os usuários<br>As opções de associação podem ser alteradas quando o grupo é criado | Abrir para ingressar para todos os usuários<br>As opções de associação podem ser alteradas quando o grupo é criado

## <a name="self-service-group-management-scenarios"></a>Cenários de gerenciamento de grupo de autoatendimento

* **Gestão de grupo delegada** Um exemplo é um administrador que está a gerir o acesso a uma aplicação SaaS que a sua empresa está a utilizar. A gestão destes direitos de acesso está a tornar-se complexa, por isso este administrador pede ao proprietário da empresa que crie um novo grupo. O administrador atribui o acesso para o aplicativo ao novo grupo e adiciona ao grupo todas as pessoas que já acessam o aplicativo. O proprietário da empresa pode então adicionar mais utilizadores e estes são aprovisionados automaticamente para a aplicação. O proprietário da empresa não tem de aguardar que o administrador faça a gestão do acesso dos utilizadores. Se o administrador conceder a mesma permissão a um gerente em um grupo de negócios diferente, essa pessoa também poderá gerenciar o acesso para seus próprios membros do grupo. Nem o proprietário da empresa nem o gerente podem exibir ou gerenciar as associações de grupo uns dos outros. O administrador ainda pode ver todos os utilizadores que têm acesso à aplicação e bloquear os direitos de acesso, se for necessário.
* **Gestão de grupo personalizada** Um exemplo deste cenário são dois utilizadores com sites do SharePoint Online que configuraram independentemente. Pretendem conceder acesso às equipas uns dos outros com os respetivos sites. Para o conseguirem, podem criar um grupo no Azure AD e no SharePoint Online cada um deles seleciona esse grupo para fornecer acesso aos respetivos sites. Quando alguém quiser ter acesso, pode solicitá-lo no Painel de Acesso e, após a aprovação, obtém automaticamente acesso a ambos os sites do SharePoint Online. Posteriormente, um deles decide que todas as pessoas que acedem ao site devem também ter acesso a uma determinada aplicação SaaS. O administrador da aplicação SaaS pode adicionar direitos de acesso da aplicação ao site do SharePoint Online. A partir daí, quaisquer pedidos que ele aprove concedem acesso aos dois sites SharePoint Online e também a esta aplicação SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Disponibilizar um grupo para personalização pelo utilizador

1. Inicie sessão no [Centro de administradores do Azure AD](https://aad.portal.azure.com) com uma conta que seja administrador global do diretório.
2. Selecione **Utilizadores e Grupos** e **Definições do grupo**.
3. Defina **Gestão de grupos personalizada ativada** como **Sim**.
4. Defina **Os utilizadores podem criar grupos de segurança** ou **Os utilizadores podem criar grupos do Office 365** como **Sim**.
   * Se estas definições estiverem ativada, todos os utilizadores do seu diretório estão autorizados a criar novos grupos de segurança e a adicionar membros aos mesmos. Estes novos grupos apareceriam também no Painel de Acesso para todos os outros utilizadores. Se a definição de política do grupo o permitir, outros utilizadores podem criar pedidos de adesão para estes grupos. 
   * Se estas definições estiverem desativadas, os utilizadores não podem criar grupos nem alterar os grupos existentes dos quais são proprietários. No entanto, podem continuar gerir os membros desses grupos e aprovar pedidos de outros utilizadores para pertencer aos respetivos grupos.

Também pode utilizar **Utilizadores que podem gerir grupos de segurança** e **Utilizadores que podem gerir grupos do Office 365** para obter um controlo de acesso mais refinado sobre a gestão de grupos personalizada para os seus utilizadores. Se **Os utilizadores podem criar grupos** estiver ativado, todos os utilizadores do seu inquilino estão autorizados a criar novos grupos e a adicionar membros aos mesmos. Você não pode especificar indivíduos que podem criar seus próprios grupos. Você pode especificar indivíduos somente para tornar outro membro de grupo um proprietário de grupo.

Ao definir **os usuários que podem usar o autoatendimento para grupos de segurança** e **usuários que podem gerenciar grupos do Office 365** como **Sim**, você permite que todos os usuários em seu locatário criem novos grupos.

Também pode utilizar **Grupo que pode gerir grupos de segurança** ou **Grupo que pode gerir grupos do Office 365** para especificar um grupo individual cujos membros podem utilizar a personalização.

## <a name="next-steps"></a>Passos Seguintes

Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

* [Manage access to resources with Azure Active Directory groups](../fundamentals/active-directory-manage-groups.md) (Gerir o acesso aos recursos com grupos do Azure Active Directory)
* [Cmdlets do Azure Active Directory para configurar definições de grupo](groups-settings-cmdlets.md)
* [Gestão de Aplicações no Azure Active Directory](../manage-apps/what-is-application-management.md)
* [O que é o Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrar as identidades no local no Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
