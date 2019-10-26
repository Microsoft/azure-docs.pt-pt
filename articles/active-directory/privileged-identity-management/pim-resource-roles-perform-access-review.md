---
title: Examinar o acesso às funções de recurso do Azure no Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Saiba como examinar o acesso das funções de recurso do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d5837bba537990f4a2b49a74a1266bd09aba0d5
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895585"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Examinar o acesso às funções de recurso do Azure no Privileged Identity Management

As revisões de acesso do Privileged Identity Management (PIM) podem ajudar a proteger o acesso a funções com privilégios no Azure Active Directory (AD do Azure). Este artigo as etapas para concluir uma análise das atribuições de função com privilégios em uma revisão de acesso do Azure AD.

Se você estiver atribuído a uma função administrativa, talvez seja necessário concluir uma análise de acesso por seu administrador para confirmar a necessidade de uma função. A solicitação de confirmação pode vir um email que inclui um link ou você pode confirmar na [portal do Azure](https://portal.azure.com).

Se você for um administrador de função com privilégios interessado em revisões de acesso, obtenha mais detalhes em [como iniciar uma revisão de acesso](pim-resource-roles-start-access-review.md).

## <a name="approve-or-deny-access"></a>Aprovar ou negar acesso

Você pode aprovar ou negar o acesso com base em se você ainda usa essa função ou não. Escolha **aprovar** se você quiser permanecer na função ou **negar** se não precisar mais do acesso. Seu status será alterado somente depois que o revisor aplicar os resultados.

Siga estas etapas para localizar e concluir a revisão de acesso:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Selecione **Azure Active Directory** e abra **Privileged Identity Management**.
1. Selecione **examinar acesso**.

   ![Captura de tela de Privileged Identity Management aplicativo, com a folha de acesso de revisão selecionada](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. Selecione a revisão que você deseja concluir.
1. Escolha **aprovar** ou **negar**. Na **caixa fornecer um motivo**, insira uma justificativa de negócios para sua decisão, se necessário.

   ![Captura de tela da página de detalhes da revisão](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Passos seguintes

- [Executar uma revisão de acesso das minhas funções do Azure AD no Privileged Identity Management](pim-how-to-perform-security-review.md)
