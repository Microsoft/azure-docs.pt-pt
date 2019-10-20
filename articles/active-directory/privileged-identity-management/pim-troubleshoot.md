---
title: Solucionar um problema com o Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Saiba como solucionar problemas de erros do sistema com funções no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: f54382d652c3fc59b8ff462d41ec8c0fbdbe8498
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596699"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Solucionar um problema com Privileged Identity Management

Você está tendo um problema com Privileged Identity Management (PIM) no Azure Active Directory (Azure AD)? As informações a seguir podem ajudá-lo a fazer as coisas funcionarem novamente.

## <a name="access-to-azure-resources-denied"></a>Acesso aos recursos do Azure negado

### <a name="problem"></a>Problema

Como um proprietário ativo ou administrador de acesso de usuário para um recurso do Azure, você pode ver seu recurso dentro de Privileged Identity Management mas não pode executar ações como fazer uma atribuição qualificada ou exibir uma lista de atribuições de função do recurso página de visão geral. Qualquer uma dessas ações resulta em um erro de autorização.

### <a name="cause"></a>Causa

Esse problema pode ocorrer quando a função Administrador de acesso do usuário para a entidade de serviço PIM foi acidentalmente removida da assinatura. Para que o serviço de Privileged Identity Management seja capaz de acessar recursos do Azure, a entidade de serviço do MS-PIM sempre deve ter atribuído a [função de administrador de acesso do usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator) pela assinatura do Azure.

### <a name="resolution"></a>Resolução

Atribua a função de administrador de acesso do usuário ao nome da entidade de serviço do Privileged Identity Management (MS – PIM) no nível da assinatura. Essa atribuição deve permitir que o serviço Privileged Identity Management acesse os recursos do Azure. A função pode ser atribuída em um nível de grupo de gerenciamento ou no nível de assinatura, dependendo de seus requisitos. Para obter mais informações sobre entidades de serviço, consulte [atribuir um aplicativo a uma função](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role).

## <a name="next-steps"></a>Passos seguintes

- [Requisitos de licença para usar o Privileged Identity Management](subscription-requirements.md)
- [Proteção de acesso privilegiado para implementações híbridas e na cloud no Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Implantar Privileged Identity Management](pim-deployment-plan.md)
