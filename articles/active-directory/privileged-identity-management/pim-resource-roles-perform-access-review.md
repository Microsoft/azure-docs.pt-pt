---
title: Reveja o acesso às funções de recursos da Azure em PIM - Azure AD | Microsoft Docs
description: Saiba como rever o acesso às funções de recursos Azure em Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8091890d174ae68f012a6ec24685a0e705100f8b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84743682"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Reveja o acesso às funções de recursos da Azure na Gestão de Identidade Privilegiada

As avaliações de acesso a Gestão de Identidade Privilegiada (PIM) podem ajudar a garantir o acesso a funções privilegiadas no Azure Ative Directory (Azure AD). Este artigo os passos para completar uma revisão das suas atribuições de papel privilegiadas numa revisão de acesso AD AZure.

Se for designado para uma função administrativa, poderá ser-lhe exigido que complete uma revisão de acesso pelo seu administrador para confirmar a sua necessidade de uma função. O pedido de confirmação pode vir de um e-mail que inclui um link, ou pode confirmar no [portal Azure.](https://portal.azure.com)

Se você é um administrador privilegiado interessado em avaliações de acesso, obtenha mais detalhes sobre [Como iniciar uma revisão de acesso](pim-resource-roles-start-access-review.md).

## <a name="approve-or-deny-access"></a>Aprovar ou negar acesso

Pode aprovar ou negar o acesso com base no facto de ainda utilizar esta função ou não. Escolha **Aprovar** se quiser continuar no papel, ou **negue** se já não precisar do acesso. O seu estado só muda depois de o revisor aplicar os resultados.

Siga estes passos para encontrar e completar a revisão de acesso:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione **Azure Ative Directory** e abra **Gestão de Identidade Privilegiada.**
1. Selecione **o acesso de Revisão**.

   ![Screenshot da aplicação de Gestão de Identidade Privilegiada, com lâmina de acesso de Revisão selecionada](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. Selecione a avaliação que pretende completar.
1. Escolha **aprovar** ou **negar.** Na **caixa Fornecer uma caixa de razão,** insira uma justificação de negócio para a sua decisão, se necessário.

   ![Screenshot da página de detalhes do Comentário](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Passos seguintes

- [Realizar uma revisão de acesso das minhas funções de Ad Azure em Gestão de Identidade Privilegiada](pim-how-to-perform-security-review.md)
