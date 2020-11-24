---
title: Utilize um grupo para gerir o acesso a apps SaaS - Azure AD ! Microsoft Docs
description: Como utilizar grupos no Azure Ative Directory para atribuir acesso a aplicações SaaS que são integradas com o Azure Ative Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73e30765a3a05f9ebae1d9fb4a57cdc198da1974
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95488593"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Utilizar um grupo para gerir o acesso a aplicações SaaS

Utilizando o Azure Ative Directory (Azure AD) com um plano de licença Azure AD Premium, pode utilizar grupos para atribuir acesso a uma aplicação SaaS que está integrada com a Azure AD. Por exemplo, se quiser atribuir acesso ao departamento de marketing para utilizar cinco aplicações SaaS diferentes, pode criar um grupo que contenha os utilizadores no departamento de marketing e, em seguida, atribuir esse grupo a estas cinco aplicações SaaS que são necessárias pelo departamento de marketing. Desta forma, você pode economizar tempo gerindo a adesão ao departamento de marketing em um lugar. Os utilizadores são então designados para a aplicação quando são adicionados como membros do grupo de marketing, e têm as suas atribuições removidas da aplicação quando são removidas do grupo de marketing. Esta capacidade pode ser utilizada com centenas de aplicações que pode adicionar a partir da Galeria de Aplicações AD AZure.

> [!IMPORTANT]
> Só pode utilizar esta funcionalidade depois de iniciar um teste Azure AD Premium ou comprar um plano de licença Azure AD Premium.
> A atribuição baseada em grupo é suportada apenas para grupos de segurança.
> Filiações aninhadas em grupos não são suportadas atualmente para uma atribuição a aplicações baseada em grupos.

## <a name="to-assign-access-for-a-user-or-group-to-a-saas-application"></a>Para atribuir acesso a um utilizador ou grupo a uma aplicação SaaS

1. No [centro de administração Admin Ad,](https://aad.portal.azure.com)selecione **aplicações Enterprise**.
2. Selecione uma aplicação que adicionou na Galeria de Aplicações para abri-la.
3. Selecione **Utilizadores e grupos** e, em seguida, selecione **Adicionar utilizador**.
4. No **Add Assignment**, selecione **Utilizadores e grupos** para abrir a lista de seleção **de Utilizadores e grupos.**
6. Selecione quantos grupos ou utilizadores quiser, clique em seguida, clique ou toque **Selecione** para adicioná-los à lista **de Atribuição de Adicionar.** Pode também atribuir uma função a um utilizador nesta fase.
7. Selecione **Atribuir** para atribuir os utilizadores ou grupos à aplicação de empresa selecionada.

## <a name="next-steps"></a>Passos seguintes
Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Gestão de Aplicações no Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Cmdlets do Azure Active Directory para configurar definições de grupo](../enterprise-users/groups-settings-cmdlets.md)
* [O que é o Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
