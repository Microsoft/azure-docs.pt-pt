---
title: Problemas de resolução de problemas com gestão de identidade privilegiada - Diretório Ativo Azure Microsoft Docs
description: Saiba como resolver erros do sistema com funções na Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 474f2634e6f7ddc1840548c39ae86cb54c3bf08e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299691"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Problemas de resolução de problemas com gestão de identidade privilegiada

Está a ter um problema com a Gestão de Identidade Privilegiada (PIM) no Diretório Ativo Azure (Azure AD)? A informação que se segue pode ajudá-lo a pôr as coisas a funcionar novamente.

## <a name="access-to-azure-resources-denied"></a>Acesso aos recursos do Azure negado

### <a name="problem"></a>Problema

Como proprietário ativo ou administrador de acesso ao utilizador para um recurso Azure, você pode ver o seu recurso dentro da Privileged Identity Management, mas não pode realizar quaisquer ações como fazer uma atribuição elegível ou visualizar uma lista de atribuições de funções do recurso página geral. Qualquer uma destas ações resulta num erro de autorização.

### <a name="cause"></a>Causa

Este problema pode acontecer quando a função do Administrador de Acesso ao Utilizador para o diretor de serviço PIM foi acidentalmente removida da subscrição. Para que o serviço de Gestão de Identidade Privilegiada possa aceder aos recursos do Azure, o diretor de serviço si-PIM deve ter sido sempre atribuído ao cargo de Administrador de [Acesso ao Utilizador](../../role-based-access-control/built-in-roles.md#user-access-administrator) sobre a subscrição do Azure.

### <a name="resolution"></a>Resolução

Atribuir a função de Administrador de Acesso ao Utilizador ao nome principal do serviço de Gestão de Identidade Privilegiada (MS-PIM) ao nível da subscrição. Esta atribuição deverá permitir ao serviço de Gestão de Identidade Privilegiada aceder aos recursos do Azure. A função pode ser atribuída a nível de grupo de gestão ou ao nível da subscrição, dependendo dos seus requisitos. Para mais diretores de serviços de informação, consulte [Atribuir uma aplicação a um papel](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application).

## <a name="next-steps"></a>Passos seguintes

- [Requisitos de licença para usar Gestão de Identidade Privilegiada](subscription-requirements.md)
- [Proteção de acesso privilegiado para implementações híbridas e na cloud no Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Implementar o Privileged Identity Management](pim-deployment-plan.md)
