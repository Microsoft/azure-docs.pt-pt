---
title: Utilize funções personalizadas para recursos Azure na PIM - Azure AD [ Microsoft Docs
description: Saiba como utilizar funções personalizadas para recursos Azure na Azure AD Privileged Identity Management (PIM).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbe08cff2b57155f8f3315f5d3454abfbdad47a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847052"
---
# <a name="use-custom-roles-for-azure-resources-in-privileged-identity-management"></a>Utilize funções personalizadas para recursos Azure na Gestão de Identidade Privilegiada

Poderá ser necessário aplicar definições estritas de Gestão de Identidade Privilegiada (PIM) a alguns utilizadores num papel privilegiado na sua organização Azure Ative Directory (Azure AD), ao mesmo tempo que proporciona maior autonomia para outros. Considere, por exemplo, um cenário em que a sua organização contrata vários associados de contrato para ajudar no desenvolvimento de uma aplicação que será executada numa subscrição azure.

Como administrador de recursos, pretende que os colaboradores sejam elegíveis para acesso sem necessidade de aprovação. No entanto, todos os associados contratuais devem ser aprovados quando solicitarem o acesso aos recursos da organização.

Siga os passos descritos na secção seguinte para configurar definições de Gestão de Identidade Privilegiada direcionadas para funções de recurso Azure.

## <a name="create-the-custom-role"></a>Criar o papel personalizado

Para criar um papel personalizado para um recurso, siga os passos descritos em [Criar papéis personalizados para o Controlo de Acesso baseado em funções azure](../role-based-access-control-custom-roles.md).

Quando criar um papel personalizado, inclua um nome descritivo para que se lembre facilmente qual o papel incorporado que pretendia duplicar.

> [!NOTE]
> Certifique-se de que o papel personalizado é uma duplicação do papel incorporado que pretende duplicar, e que o seu âmbito corresponde ao papel incorporado.

## <a name="apply-pim-settings"></a>Aplicar as definições pim

Depois de o papel ser criado na sua organização Azure AD, vá à página de Gestão de **Identidade Privilegiada - Azure** no portal Azure. Selecione o recurso a que a função se aplica.

![O painel "Privilegiada gestão de identidade - recursos Azure"](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Configure as definições](pim-resource-roles-configure-role-settings.md) de funções de Gestão de Identidade Privilegiada que devem aplicar-se a estes membros da função.

Por fim, [atribua funções](pim-resource-roles-assign-roles.md) ao grupo distinto de membros que pretende atingir com estas definições.

## <a name="next-steps"></a>Passos seguintes

- [Configure definições de funções de recursos Azure na Gestão de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)
- [Custom roles in Azure](../../role-based-access-control/custom-roles.md) (Funções personalizadas no Azure)
