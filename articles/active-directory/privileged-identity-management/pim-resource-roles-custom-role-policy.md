---
title: Use funções personalizadas Azure em PIM - Azure AD | Microsoft Docs
description: Saiba como utilizar funções personalizadas Azure na Azure AD Privileged Identity Management (PIM).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24b7845ec66a85e6ced4f1df9caec409a94016bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88782605"
---
# <a name="use-azure-custom-roles-in-privileged-identity-management"></a>Use funções personalizadas Azure na Gestão de Identidade Privilegiada

Poderá ter de aplicar configurações estritas de Gestão de Identidade Privilegiada (PIM) a alguns utilizadores num papel privilegiado na sua organização Azure Ative Directory (Azure AD), ao mesmo tempo que proporciona uma maior autonomia para outros. Considere, por exemplo, um cenário em que a sua organização contrata vários associados contratuais para ajudar no desenvolvimento de uma aplicação que será executada numa subscrição do Azure.

Como administrador de recursos, pretende que os colaboradores sejam elegíveis para acesso sem necessidade de aprovação. No entanto, todos os associados contratados devem ser aprovados quando solicitarem o acesso aos recursos da organização.

Siga os passos descritos na secção seguinte para configurar configurações de Gestão de Identidade Privilegiada direcionadas para funções de recursos Azure.

## <a name="create-the-custom-role"></a>Criar o papel personalizado

Para criar um papel personalizado para um recurso, siga os passos descritos nas [funções personalizadas Azure](../../role-based-access-control/custom-roles.md).

Quando criar um papel personalizado, inclua um nome descritivo para que possa facilmente lembrar qual o papel incorporado que pretendia duplicar.

> [!NOTE]
> Certifique-se de que o papel personalizado é uma duplicação do papel incorporado que pretende duplicar e que o seu âmbito corresponde ao papel incorporado.

## <a name="apply-pim-settings"></a>Aplicar configurações PIM

Depois de o papel ser criado na sua organização Azure AD, vá para a página **de Recursos Privilegiados de Gestão de Identidade - Azure** no portal Azure. Selecione o recurso a que a função se aplica.

![O painel "Gestão de Identidade Privilegiada - Recursos Azuis"](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Configure definições de funções de gestão de identidade privilegiada](pim-resource-roles-configure-role-settings.md) que devem ser aplicáveis a estes membros do papel.

Por fim, [atribua funções](pim-resource-roles-assign-roles.md) ao distinto grupo de membros que pretende direcionar com estas definições.

## <a name="next-steps"></a>Passos seguintes

- [Configurar configurações de funções de recursos Azure na Gestão de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)
- [Custom roles in Azure](../../role-based-access-control/custom-roles.md) (Funções personalizadas no Azure)