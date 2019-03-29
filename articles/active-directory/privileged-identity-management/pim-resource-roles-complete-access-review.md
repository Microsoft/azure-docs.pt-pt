---
title: Concluir uma revisão de acesso para funções de recursos do Azure no PIM - Azure Active Directory | Documentos da Microsoft
description: Aprenda como concluir uma revisão de acesso para funções de recursos do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 837be3ccdbd133f6bf372779dadff4d3c574a2e8
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578242"
---
# <a name="complete-an-access-review-for-azure-resource-roles-in-pim"></a>Concluir uma revisão de acesso para funções de recursos do Azure no PIM
Os administradores de função com privilégios, podem rever acesso privilegiado após uma [revisão de acesso foi iniciada](pim-resource-roles-start-access-review.md). O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) envia automaticamente um e-mail que solicita aos usuários para rever o acesso. Se um utilizador não receber uma mensagem de e-mail, pode enviar-lhes as instruções [como realizar uma revisão de acesso](pim-resource-roles-perform-access-review.md).

Após o período de revisão de acesso, ou depois que todos os utilizadores tiverem terminado seus Self-rever, siga os passos neste artigo para gerir a revisão e ver os resultados.

## <a name="manage-access-reviews"></a>Gerir as revisões de acesso
1. Aceda ao [Portal do Azure](https://portal.azure.com/). Em seguida, no dashboard, selecione o **recursos do Azure** aplicação.

2. Selecione o recurso.

3. Selecione o **as revisões de acesso** secção do dashboard.
![Revisões de acesso](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)

4. Selecione a revisão de acesso que pretende gerir.

No painel de detalhes da revisão de acesso, existem várias opções para gerir essa revisão. As opções são os seguintes:

![Opções para gerir uma revisão](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Parar
Todas as revisões de acesso tem uma data de fim, mas pode utilizar o **parar** botão para terminá-lo no início. Todos os utilizadores que ainda não concluiu a revisão, a esta altura não será possível terminar depois de parar a revisão. Não é possível reiniciar uma revisão depois de interrompida.

### <a name="reset"></a>Repor
Pode redefinir uma revisão de acesso para remover todas as decisões que são feitas no mesmo. Depois de ter de repor uma revisão de acesso, todos os utilizadores são marcados como não revistos novamente. 

### <a name="apply"></a>Aplicar
Depois de uma revisão de acesso estiver concluída, utilize o **aplicar** botão para implementar o resultado da revisão. Se foi negado o acesso de um utilizador da revisão, este passo remove a respetiva atribuição de função.  

### <a name="delete"></a>Eliminar
Se não estiver interessado na revisão mais, eliminá-lo. O **eliminar** botão remove a revisão da aplicação do PIM.

## <a name="results"></a>Resultados
Sobre o **resultados** separador, ver e transferir uma lista de resultados de revisão. 
![Separador de resultados](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>Revisores
Ver e adicionar revisores a sua revisão de acesso existentes. Notificar os revisores para concluir as revisões.
![Adicionar revisores](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Passos Seguintes

- [Inicie uma revisão de acesso das funções de recursos no PIM](pim-resource-roles-start-access-review.md)
- [Efetuar uma revisão de acesso das minhas funções de recurso no PIM](pim-resource-roles-perform-access-review.md)
