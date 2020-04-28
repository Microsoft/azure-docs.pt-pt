---
title: Avaliação de acesso ao acabamento das funções de recurso Azure na PIM - Azure AD [ Microsoft Docs
description: Saiba como concluir uma revisão de acesso das funções de recurso Azure Privileged Identity Management in Azure Ative Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e45249245aaab97070b7e774d4b6bab6827bdc9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74021988"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Complete uma revisão de acesso das funções de recurso Azure na Gestão de Identidade Privilegiada

Os administradores privilegiados podem rever o acesso privilegiado após a sua revisão de [acesso.](pim-resource-roles-start-access-review.md) A Privileged Identity Management (PIM) no Azure Ative Directory (Azure AD) envia automaticamente um e-mail que leva os utilizadores a rever em seu acesso. Se um utilizador não receber um e-mail, pode enviar-lhes as instruções para [como efetuar uma revisão](pim-resource-roles-perform-access-review.md)de acesso .

Após o fim do período de revisão de acesso, ou depois de todos os utilizadores terem terminado a sua auto-revisão, siga os passos deste artigo para gerir a revisão e ver os resultados.

## <a name="manage-access-reviews"></a>Gerir avaliações de acesso

1. Vá ao [portal Azure.](https://portal.azure.com/) No painel de instrumentos, selecione o serviço de **recursos Azure.**

2. Selecione o seu recurso.

3. Selecione a secção de comentários de **acesso** do painel de instrumentos.

    ![Recursos Azure - Lista de avaliações de acesso mostrando papel, proprietário, data de início, data de fim e estado](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Selecione a revisão de acesso que pretende gerir.

Na página de detalhes para a revisão de acesso, existem várias opções para gerir essa revisão. As opções são as seguintes:

![Opções para gerir uma revisão - Parar, Redefinir, Aplicar, Excluir](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Parar

Todas as avaliações de acesso têm uma data limite. Selecione **Stop** para terminá-lo mais cedo. Qualquer utilizador que ainda não tenha terminado a sua análise até ao momento não poderá terminá-la depois de parar a revisão. Não pode reiniciar uma revisão depois de ter sido interrompida.

### <a name="reset"></a>Repor

Pode redefinir uma revisão de acesso para remover todas as decisões que são tomadas. Depois de redefinir uma revisão de acesso, todos os utilizadores estão marcados como não revistos novamente.

### <a name="apply"></a>Aplicar

Depois de concluída uma revisão de acesso, selecione **Aplicar** para implementar o resultado da revisão. Se o acesso de um utilizador foi negado na revisão, este passo remove a sua atribuição de funções.  

### <a name="delete"></a>Eliminar

Se já não estiver interessado na revisão, apague-a. **Selecione Eliminar** yo remova a revisão do serviço de Gestão de Identidade Privilegiada.

## <a name="results"></a>Resultados

Na página **resultados,** veja e baixe uma lista dos resultados da sua análise.

![Página de resultados listando utilizadores, resultado, razão, revisto por, aplicado por, e aplicar resultado](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Revisores

Ver e adicionar revisores à sua revisão de acesso existente. Lembre os revisores para completar as suas avaliações.

![Página de revisores nome de listagem e nome principal do utilizador](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Passos seguintes

- [Inicie uma revisão de acesso para funções de recursos Azure na Gestão de Identidade Privilegiada](pim-resource-roles-start-access-review.md)
- [Realizar uma revisão de acesso das minhas funções de recurso Azure na Gestão de Identidade Privilegiada](pim-resource-roles-perform-access-review.md)
