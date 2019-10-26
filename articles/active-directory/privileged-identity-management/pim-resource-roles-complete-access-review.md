---
title: Concluir uma revisão de acesso das funções de recurso do Azure no Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Saiba como concluir uma revisão de acesso das funções de recurso do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6761d1d6aea796e0cca708676f69c47328642cc2
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895814"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Concluir uma revisão de acesso das funções de recurso do Azure no Privileged Identity Management

Os administradores de função com privilégios podem revisar o acesso privilegiado depois [de iniciarem uma revisão de acesso](pim-resource-roles-start-access-review.md). O Privileged Identity Management (PIM) no Azure Active Directory (Azure AD) envia automaticamente um email que solicita aos usuários que revisem seu acesso. Se um usuário não receber um email, você poderá enviar a eles as instruções de [como executar uma revisão de acesso](pim-resource-roles-perform-access-review.md).

Depois que o período de revisão de acesso terminar, ou depois que todos os usuários concluírem sua autorevisão, siga as etapas neste artigo para gerenciar a revisão e ver os resultados.

## <a name="manage-access-reviews"></a>Gerenciar revisões de acesso

1. Aceda ao [Portal do Azure](https://portal.azure.com/). No painel, selecione o serviço **recursos do Azure** .

2. Selecione seu recurso.

3. Selecione a seção **revisões de acesso** do painel.

    ![Recursos do Azure-lista de revisões de acesso mostrando a função, o proprietário, a data de início, a data de término e o status](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Selecione a revisão de acesso que você deseja gerenciar.

Na página de detalhes da revisão de acesso, há várias opções para gerenciar essa revisão. As opções são as seguintes:

![Opções para gerenciar uma análise-parar, redefinir, aplicar, excluir](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Parar

Todas as revisões de acesso têm uma data de término. Selecione **parar** para concluir o início. Todos os usuários que não concluíram sua revisão neste momento não poderão concluí-lo depois que você parar a revisão. Não é possível reiniciar uma revisão após ela ter sido interrompida.

### <a name="reset"></a>Repor

Você pode redefinir uma revisão de acesso para remover todas as decisões que são feitas nela. Depois de redefinir uma revisão de acesso, todos os usuários serão marcados como não revisados novamente.

### <a name="apply"></a>Registe-se

Após a conclusão de uma revisão de acesso, selecione **aplicar** para implementar o resultado da revisão. Se o acesso de um usuário tiver sido negado na revisão, essa etapa removerá sua atribuição de função.  

### <a name="delete"></a>Eliminar

Se você não estiver interessado na revisão de mais, exclua-o. Selecione **excluir** Yo remover a revisão do serviço de Privileged Identity Management.

## <a name="results"></a>Resultados

Na página **resultados** , exiba e Baixe uma lista de seus resultados de revisão.

![Página de resultados listando usuários, resultado, motivo, revisado por, aplicado por e aplicar resultado](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Revisores

Exiba e adicione revisores à sua análise de acesso existente. Lembre os revisores para concluir suas revisões.

![Nome de listagem da página de revisores e nome principal do usuário](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Passos seguintes

- [Iniciar uma revisão de acesso para funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-start-access-review.md)
- [Executar uma revisão de acesso das minhas funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-perform-access-review.md)
