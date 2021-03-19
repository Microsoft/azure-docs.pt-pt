---
title: Termine a revisão de acesso das funções de recursos Azure em PIM - Azure AD | Microsoft Docs
description: Saiba como completar uma revisão de acesso das funções de recursos Azure Gestão de Identidade Privilegiada no Diretório Ativo Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1601c9fc750df1f8e18bdaa072ede1b5ee164329
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84743818"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Complete uma revisão de acesso das funções de recursos da Azure na Gestão de Identidade Privilegiada

Os administradores privilegiados podem rever o acesso privilegiado depois de [iniciarem uma revisão de acesso.](pim-resource-roles-start-access-review.md) Gestão de Identidade Privilegiada (PIM) em Azure Ative Directory (Azure AD)envia automaticamente um e-mail que pede aos utilizadores que revejam o seu acesso. Se um utilizador não receber um e-mail, pode enviar-lhe as instruções para [como realizar uma revisão de acesso](pim-resource-roles-perform-access-review.md).

Após o fim do período de revisão de acesso, ou depois de todos os utilizadores terem terminado a sua auto-revisão, siga os passos deste artigo para gerir a revisão e ver os resultados.

## <a name="manage-access-reviews"></a>Gerir comentários de acesso

1. Aceda ao [Portal do Azure](https://portal.azure.com/). No painel de instrumentos, selecione o serviço **de recursos Azure.**

2. Selecione o seu recurso.

3. Selecione a secção **de avaliações** de Acesso do painel de instrumentos.

    ![Recursos Azure - Lista de comentários de acesso mostrando função, proprietário, data de início, data de fim e estado](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Selecione a revisão de acesso que pretende gerir.

Na página de detalhes para a revisão de acesso, existem várias opções para gerir essa revisão. As opções são as seguintes:

![Opções para gerir uma revisão - Parar, Redefinir, Aplicar, Eliminar](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Parar

Todas as avaliações de acesso têm uma data limite. **Selecione Stop** para terminá-lo mais cedo. Todos os utilizadores que ainda não terminarem a sua análise não poderão terminá-la depois de parar a revisão. Não pode reiniciar uma revisão depois de ter sido interrompida.

### <a name="reset"></a>Repor

Pode redefinir uma revisão de acesso para remover todas as decisões que forem tomadas sobre o mesmo. Depois de ter reiniciado uma revisão de acesso, todos os utilizadores são marcados como não revistos novamente.

### <a name="apply"></a>Aplicar

Depois de concluída uma revisão de acesso, **selecione Aplicar** para implementar o resultado da revisão. Se o acesso de um utilizador foi negado na revisão, este passo remove a sua atribuição de funções.  

### <a name="delete"></a>Eliminar

Se já não está interessado na revisão, apague-a. **Selecione Delete** yo remover o comentário do serviço de Gestão de Identidade Privilegiada.

## <a name="results"></a>Resultados

Na página **Resultados,** veja e descarregue uma lista dos resultados da sua avaliação.

![Página de resultados listando utilizadores, resultados, razão, revistos por, aplicados e aplicam resultado](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Revisores

Ver e adicionar revisores à sua análise de acesso existente. Lembre os revisores para completarem as suas críticas.

![Nome da listagem de página dos revisores e nome principal do utilizador](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Passos seguintes

- [Inicie uma revisão de acesso para funções de recursos Azure na Gestão de Identidade Privilegiada](pim-resource-roles-start-access-review.md)
- [Realizar uma revisão de acesso das minhas funções de recursos Azure em Gestão de Identidade Privilegiada](pim-resource-roles-perform-access-review.md)
