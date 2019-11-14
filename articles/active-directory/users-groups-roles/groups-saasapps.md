---
title: Usar um grupo para gerenciar o acesso a aplicativos SaaS-Azure AD | Microsoft Docs
description: Como usar grupos no Azure Active Directory para atribuir acesso a aplicativos SaaS que são integrados com o Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51375f057543c86fe021822eb9722ffd1be16804
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74026845"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Utilizar um grupo para gerir o acesso a aplicações SaaS

Usando o Azure Active Directory (Azure AD) com um plano de licença de Azure AD Premium, você pode usar grupos para atribuir acesso a um aplicativo SaaS integrado ao Azure AD. Por exemplo, se você quiser atribuir acesso ao departamento de marketing para usar cinco aplicativos SaaS diferentes, poderá criar um grupo que contenha os usuários no departamento de marketing e, em seguida, atribuir esse grupo a esses cinco aplicativos SaaS que são necessários para o departamento de marketing. Dessa forma, você pode economizar tempo gerenciando a associação do departamento de marketing em um único lugar. Os usuários são atribuídos ao aplicativo quando são adicionados como membros do grupo de marketing e têm suas atribuições removidas do aplicativo quando eles são removidos do grupo de marketing. Esse recurso pode ser usado com centenas de aplicativos que você pode adicionar de dentro da Galeria de aplicativos do Azure AD.

> [!IMPORTANT]
> Você pode usar esse recurso somente depois de iniciar uma Azure AD Premium avaliação ou compra Azure AD Premium plano de licença.
> A atribuição baseada em grupo tem suporte apenas para grupos de segurança.
> Filiações aninhadas em grupos não são suportadas atualmente para uma atribuição a aplicações baseada em grupos.

## <a name="to-assign-access-for-a-user-or-group-to-a-saas-application"></a>Para atribuir acesso a um usuário ou grupo a um aplicativo SaaS

1. No [centro de administração do Azure ad](https://aad.portal.azure.com), selecione **aplicativos empresariais**.
2. Selecione um aplicativo que você adicionou da Galeria de aplicativos para abri-lo.
3. Selecione **usuários e grupos**e, em seguida, selecione **Adicionar usuário**.
4. Em **Adicionar atribuição**, selecione **usuários e grupos** para abrir a lista de seleção **usuários e grupos** .
6. Selecione quantos grupos ou usuários desejar e, em seguida, clique ou toque em **selecionar** para adicioná-los à lista **Adicionar atribuição** . Você também pode atribuir uma função a um usuário neste estágio.
7. Selecione **atribuir** para atribuir os usuários ou grupos ao aplicativo empresarial selecionado.

## <a name="next-steps"></a>Passos seguintes
Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Gestão de Aplicações no Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Cmdlets do Azure Active Directory para configurar definições de grupo](groups-settings-cmdlets.md)
* [O que é o Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
