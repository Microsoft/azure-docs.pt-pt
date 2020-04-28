---
title: Utilize um grupo para gerir o acesso a apps SaaS - Azure AD [ Microsoft Docs
description: Como utilizar grupos no Diretório Ativo Azure para atribuir acesso a aplicações SaaS que estão integradas com o Diretório Ativo Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74026845"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Utilizar um grupo para gerir o acesso a aplicações SaaS

Utilizando o Azure Ative Directory (Azure AD) com um plano de licença Azure AD Premium, pode utilizar grupos para atribuir acesso a uma aplicação SaaS integrada com a Azure AD. Por exemplo, se quiser atribuir acesso ao departamento de marketing para utilizar cinco aplicações SaaS diferentes, pode criar um grupo que contenha os utilizadores no departamento de marketing e, em seguida, atribuir esse grupo a estas cinco aplicações SaaS que são necessárias pelo departamento de marketing. Desta forma, pode economizar tempo gerindo a adesão ao departamento de marketing num só local. Os utilizadores são então atribuídos à aplicação quando são adicionados como membros do grupo de marketing, e têm as suas atribuições removidas da aplicação quando são removidas do grupo de marketing. Esta capacidade pode ser utilizada com centenas de aplicações que pode adicionar dentro da Galeria de Aplicações AD Azure.

> [!IMPORTANT]
> Só pode utilizar esta funcionalidade depois de iniciar um teste Azure AD Premium ou adquirir um plano de licença Azure AD Premium.
> A atribuição baseada em grupo é apoiada apenas para grupos de segurança.
> Filiações aninhadas em grupos não são suportadas atualmente para uma atribuição a aplicações baseada em grupos.

## <a name="to-assign-access-for-a-user-or-group-to-a-saas-application"></a>Para atribuir acesso a um utilizador ou grupo a uma aplicação SaaS

1. No [centro de administração da Azure AD,](https://aad.portal.azure.com)selecione **aplicações Enterprise**.
2. Selecione uma aplicação que adicionou na Galeria de Aplicações para a abrir.
3. **Selecione Utilizadores e grupos**, e, em seguida, selecione **Adicionar utilizador**.
4. Em **Adicionar Atribuição,** selecione **Utilizadores e grupos** para abrir a lista de seleção de **Utilizadores e grupos.**
6. Selecione quantos grupos ou utilizadores quiser, clique ou toque em **Select** para adicioná-los à lista **de Atribuição de Adicionar.** Também pode atribuir uma função a um utilizador nesta fase.
7. Selecione **Atribuir** para atribuir os utilizadores ou grupos à aplicação de empresa selecionada.

## <a name="next-steps"></a>Passos seguintes
Estes artigos fornecem informações adicionais acerca do Azure Active Directory.

* [Gerir o acesso aos recursos com grupos do Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Gestão de Aplicações no Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Cmdlets do Azure Active Directory para configurar definições de grupo](groups-settings-cmdlets.md)
* [O que é o Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
