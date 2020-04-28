---
title: Rever o acesso às funções de recurso azure na PIM - Azure AD [ Microsoft Docs
description: Saiba como rever o acesso às funções de recurso Azure na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ddb4d0ff1339f1427c5041528cdbe464a345b37
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73847007"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Rever o acesso às funções de recurso Azure na Gestão de Identidade Privilegiada

As avaliações de acesso privilegiadas à Gestão de Identidade (PIM) podem ajudar a garantir o acesso a papéis privilegiados no Azure Ative Directory (Azure AD). Este artigo são os passos para concluir uma revisão das suas atribuições privilegiadas numa revisão de acesso a AD Azure.

Se for designado para um papel administrativo, poderá ser-lhe exigido que complete uma revisão de acesso pelo seu administrador para confirmar a sua necessidade de um papel. O pedido de confirmação pode chegar a um email que inclui um link, ou pode confirmar no [portal Azure](https://portal.azure.com).

Se é um administrador privilegiado interessado em avaliações de acesso, obtenha mais detalhes em [Como iniciar uma revisão](pim-resource-roles-start-access-review.md)de acesso .

## <a name="approve-or-deny-access"></a>Aprovar ou negar o acesso

Pode aprovar ou negar o acesso com base no facto de ainda utilizar este papel ou não. Escolha **Aprovar** se quiser permanecer no papel, ou **negá-lo** se já não precisar de acesso. O seu estado só muda depois de o revisor aplicar os resultados.

Siga estes passos para encontrar e completar a revisão de acesso:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione **Diretório Ativo Azure** e abra a Gestão de **Identidade Privilegiada.**
1. Selecione **Rever o acesso**.

   ![Screenshot da aplicação de Gestão de Identidade Privilegiada, com lâmina de acesso rever selecionada](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. Selecione a avaliação que pretende completar.
1. Escolha **Aprovar** ou **Negar**. Na **caixa de motivos Provide,** insira uma justificação de negócio para a sua decisão, se necessário.

   ![Screenshot da página de detalhes da revisão](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Passos seguintes

- [Realizar uma revisão de acesso das minhas funções de AD Azure na Gestão de Identidade Privilegiada](pim-how-to-perform-security-review.md)
