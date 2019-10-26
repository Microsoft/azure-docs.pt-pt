---
title: Usar funções personalizadas para recursos do Azure em Privileged Identity Management Azure Active Directory | Microsoft Docs
description: Saiba como usar funções personalizadas para recursos do Azure no Azure AD Privileged Identity Management (PIM).
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
ms.date: 10/23/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e940dd0e4c653ed4921a9e8f245aab34107ba95
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895718"
---
# <a name="use-custom-roles-for-azure-resources-in-privileged-identity-management"></a>Usar funções personalizadas para recursos do Azure no Privileged Identity Management

Talvez seja necessário aplicar configurações de PIM (Privileged Identity Management estrita) a alguns usuários em uma função privilegiada em sua organização do Azure Active Directory (Azure AD), fornecendo, ao mesmo tempo, autonomia maior para outras pessoas. Considere, por exemplo, um cenário em que sua organização contrata vários associados de contrato para auxiliar no desenvolvimento de um aplicativo que será executado em uma assinatura do Azure.

Como administrador de recursos, você deseja que os funcionários estejam qualificados para acesso sem a necessidade de aprovação. No entanto, todos os associados de contrato devem ser aprovados quando solicitam acesso aos recursos da organização.

Siga as etapas descritas na próxima seção para definir as configurações de Privileged Identity Management direcionadas para funções de recurso do Azure.

## <a name="create-the-custom-role"></a>Criar a função personalizada

Para criar uma função personalizada para um recurso, siga as etapas descritas em [criar funções personalizadas para o controle de acesso baseado em função do Azure](../role-based-access-control-custom-roles.md).

Ao criar uma função personalizada, inclua um nome descritivo para que você possa se lembrar facilmente da função interna que pretende duplicar.

> [!NOTE]
> Verifique se a função personalizada é uma duplicata da função interna que você deseja duplicar e se seu escopo corresponde à função interna.

## <a name="apply-pim-settings"></a>Aplicar configurações de PIM

Depois que a função for criada em sua organização do Azure AD, vá para a página **Privileged Identity Management-recursos do Azure** no portal do Azure. Selecione o recurso ao qual a função se aplica.

![O painel "Privileged Identity Management-recursos do Azure"](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Defina Privileged Identity Management configurações de função](pim-resource-roles-configure-role-settings.md) que devem ser aplicadas a esses membros da função.

Por fim, [atribua funções](pim-resource-roles-assign-roles.md) ao grupo distinto de membros que você deseja direcionar com essas configurações.

## <a name="next-steps"></a>Passos seguintes

- [Definir configurações de função de recurso do Azure no Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Custom roles in Azure](../../role-based-access-control/custom-roles.md) (Funções personalizadas no Azure)
