---
title: Resolução de problemas com Gestão de Identidade Privilegiada - Diretório Ativo Azure Microsoft Docs
description: Saiba como resolver erros do sistema com funções na Gestão de Identidade Privilegiada Azure AD (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcbebcb32e912abdf0112007c743c6890fae36e4
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372400"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Resolver problemas com gestão de identidade privilegiada

Está a ter algum problema com a Gestão de Identidade Privilegiada (PIM) no Azure Ative Directory (Azure AD)? A informação que se segue pode ajudá-lo a pôr as coisas a funcionar novamente.

## <a name="access-to-azure-resources-denied"></a>Acesso aos recursos da Azure negado

### <a name="problem"></a>Problema

Como proprietário ativo ou administrador de acesso ao utilizador para um recurso Azure, é capaz de ver o seu recurso dentro da Gestão de Identidade Privilegiada, mas não pode realizar quaisquer ações como fazer uma atribuição elegível ou visualizar uma lista de atribuições de funções a partir da página geral do recurso. Qualquer uma destas ações resulta num erro de autorização.

### <a name="cause"></a>Causa

Este problema pode ocorrer quando a função de Administrador de Acesso ao Utilizador para o principal do serviço PIM foi acidentalmente removida da subscrição. Para que o serviço de Gestão de Identidade Privilegiada possa aceder aos recursos da Azure, o diretor de serviço MS-PIM deveria ter sempre sido atribuído à [função de Administrador de Acesso](../../role-based-access-control/built-in-roles.md#user-access-administrator) ao Utilizador sobre a subscrição do Azure.

### <a name="resolution"></a>Resolução

Atribua a função de Administrador de Acesso ao Utilizador ao nome principal do serviço de gestão de identidade privilegiado (MS-PIM) ao nível da subscrição. Esta atribuição deve permitir ao serviço privilegiado de Gestão de Identidade aceder aos recursos do Azure. A função pode ser atribuída a nível de grupo de gestão ou ao nível da subscrição, dependendo dos seus requisitos. Para obter mais informações, consulte [Atribuir uma aplicação a uma função.](../develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)

## <a name="next-steps"></a>Passos seguintes

- [Requisitos de licença para utilizar a Gestão de Identidade Privilegiada](subscription-requirements.md)
- [Proteção de acesso privilegiado para implementações híbridas e na cloud no Azure AD](../roles/security-planning.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Implementar o Privileged Identity Management](pim-deployment-plan.md)