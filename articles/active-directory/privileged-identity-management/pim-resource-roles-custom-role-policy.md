---
title: Utilize as funções personalizadas para recursos do Azure no PIM - Azure Active Directory | Documentos da Microsoft
description: Saiba como utilizar funções personalizadas para recursos do Azure no Azure AD Privileged Identity Management (PIM).
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
ms.date: 03/30/2018
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e814cde49374b52266f725b4d57657a507874ab
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602042"
---
# <a name="use-custom-roles-for-azure-resources-in-pim"></a>Utilize as funções personalizadas para recursos do Azure no PIM

Poderá ter de aplicar definições strict do Azure Active Directory (Azure AD) Privileged Identity Management (PIM) para alguns membros de uma função, oferecendo maior autonomia para outras pessoas. Considere um cenário em que a sua organização contrata vários de contrato de associados, para ajudar no desenvolvimento de um aplicativo que serão executados numa subscrição do Azure.

Enquanto administrador de recursos, quer que os funcionários sejam elegíveis para o acesso sem a necessidade de aprovação. No entanto, todos os associates do contrato devem ser aprovados quando solicitam acesso aos recursos da organização.

Siga os passos descritos na secção seguinte para configurar o destinadas definições do PIM para funções de recursos do Azure.

## <a name="create-the-custom-role"></a>Criar a função personalizada

Para criar uma função personalizada para um recurso, siga os passos descritos em [criar funções personalizadas para controlo de acesso](../role-based-access-control-custom-roles.md).

Ao criar a função personalizada, inclua um nome descritivo para que pode facilmente Lembre-se de que a função incorporada que pretende duplicar.

> [!NOTE]
> Certifique-se de que a função personalizada é um duplicado da função incorporada que pretende duplicar e, se seu escopo corresponde da função incorporada.

## <a name="apply-pim-settings"></a>Aplicar definições do PIM

Depois da função é criada no seu inquilino, no portal do Azure, vá para o **Privileged Identity Management - recursos do Azure** painel. Selecione o recurso que se aplica a função.

![O "Privileged Identity Management - recursos do Azure" painel](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Configurar as definições de função do PIM](pim-resource-roles-configure-role-settings.md) que deve ser aplicada a esses membros da função.

Por fim, [atribuir funções](pim-resource-roles-assign-roles.md) ao grupo distinto de membros que pretende direcionar com estas definições.

## <a name="next-steps"></a>Passos Seguintes

- [Configurar as definições de função de recursos do Azure no PIM](pim-resource-roles-configure-role-settings.md)
- [Custom roles in Azure](../../role-based-access-control/custom-roles.md) (Funções personalizadas no Azure)
