---
title: Concluir uma revisão de acesso das funções de recurso do Azure no PIM-Azure Active Directory | Microsoft Docs
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
ms.date: 04/02/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b9563a4032011b999bf867fc782ba4cbb9c3fac
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804253"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-pim"></a>Concluir uma revisão de acesso das funções de recurso do Azure no PIM
Os administradores de função com privilégios podem examinar o acesso privilegiado após o início de uma [revisão de acesso](pim-resource-roles-start-access-review.md). O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) envia automaticamente um email que solicita aos usuários que revisem seu acesso. Se um usuário não receber um email, você poderá enviar a eles as instruções de [como executar uma revisão de acesso](pim-resource-roles-perform-access-review.md).

Depois que o período de revisão de acesso terminar, ou depois que todos os usuários concluírem sua autorevisão, siga as etapas neste artigo para gerenciar a revisão e ver os resultados.

## <a name="manage-access-reviews"></a>Gerenciar revisões de acesso
1. Aceda ao [Portal do Azure](https://portal.azure.com/). Em seguida, no painel, selecione o aplicativo **recursos do Azure** .

2. Selecione seu recurso.

3. Selecione a seção **revisões de acesso** do painel.

    ![Recursos do Azure-lista de revisões de acesso mostrando a função, o proprietário, a data de início, a data de término e o status](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Selecione a revisão de acesso que você deseja gerenciar.

Na folha de detalhes da revisão de acesso, há várias opções para gerenciar essa revisão. As opções são as seguintes:

![Opções para gerenciar uma análise-parar, redefinir, aplicar, excluir](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Parar
Todas as revisões de acesso têm uma data de término, mas você pode usar o botão **parar** para concluí-la antecipadamente. Todos os usuários que ainda não concluíram sua revisão neste momento não poderão concluí-lo depois que você parar a revisão. Não é possível reiniciar uma revisão após ela ter sido interrompida.

### <a name="reset"></a>Redefinir
Você pode redefinir uma revisão de acesso para remover todas as decisões que são feitas nela. Depois de redefinir uma revisão de acesso, todos os usuários serão marcados como não revisados novamente. 

### <a name="apply"></a>Aplicar
Após a conclusão de uma revisão de acesso, use o botão **aplicar** para implementar o resultado da revisão. Se o acesso de um usuário tiver sido negado na revisão, essa etapa removerá sua atribuição de função.  

### <a name="delete"></a>Eliminar
Se você não estiver interessado na revisão de mais, exclua-o. O botão **excluir** remove a revisão do aplicativo PIM.

## <a name="results"></a>Resultados
Na página **resultados** , exiba e Baixe uma lista de seus resultados de revisão. 

![Página de resultados listando usuários, resultado, motivo, revisado por, aplicado por e aplicar resultado](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Revisores
Exiba e adicione revisores à sua análise de acesso existente. Lembre os revisores para concluir suas revisões.

![Nome de listagem da página de revisores e nome principal do usuário](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Passos Seguintes

- [Inicie uma revisão de acesso das funções de recursos no PIM](pim-resource-roles-start-access-review.md)
- [Efetuar uma revisão de acesso das minhas funções de recurso no PIM](pim-resource-roles-perform-access-review.md)
