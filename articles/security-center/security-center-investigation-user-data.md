---
title: Gerenciar dados de usuário encontrados em uma investigação da central de segurança do Azure
description: " Saiba como gerenciar dados de usuário encontrados no recurso de investigação da central de segurança do Azure. "
services: operations-management-suite
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: memildin
ms.openlocfilehash: 9e4c6577f0b8b18aff343ac54b31ff292632f5d0
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979226"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Gerenciar dados de usuário encontrados em uma investigação da central de segurança do Azure
Este artigo fornece informações sobre como gerenciar os dados de usuário encontrados no recurso de investigação da central de segurança do Azure. Os dados de investigação são armazenados em [logs de Azure monitor](../log-analytics/log-analytics-overview.md) e expostos na central de segurança. O gerenciamento de dados do usuário inclui a capacidade de excluir ou exportar dados.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Procurar e identificar dados pessoais
No portal do Azure, você pode usar o [recurso de investigação](../security-center/security-center-investigation.md) da central de segurança para pesquisar dados pessoais. O recurso de investigação está disponível em **alertas de segurança**.

O recurso de investigação mostra todas as entidades, informações de usuário e dados na guia **entidades** .

## <a name="securing-and-controlling-access-to-personal-information"></a>Proteger e controlar o acesso a informações pessoais
Um usuário da central de segurança atribuiu a função de leitor, proprietário, colaborador ou administrador da conta pode acessar dados do cliente dentro da ferramenta.

Consulte [funções internas para o controle de acesso baseado em função do Azure](../role-based-access-control/built-in-roles.md) para saber mais sobre as funções de leitor, proprietário e colaborador. Consulte [Administradores de assinatura do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md) para saber mais sobre a função de administrador da conta.

## <a name="deleting-personal-data"></a>A eliminar os dados pessoais
Um usuário da central de segurança que atribuiu a função de proprietário, colaborador ou administrador da conta pode excluir as informações de investigação.

Para excluir uma investigação, você pode enviar uma solicitação de `DELETE` para a API REST do Azure Resource Manager:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

A entrada `incidentName` pode ser encontrada listando todos os incidentes usando uma solicitação `GET`:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Exportar os dados pessoais
Um usuário da central de segurança que atribuiu a função de proprietário, colaborador ou administrador da conta pode exportar as informações de investigação. Para exportar informações de investigação, vá para a guia **entidades** para copiar e colar as informações relevantes.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como gerenciar dados do usuário, consulte [gerenciar dados do usuário na central de segurança do Azure](security-center-privacy.md).
Para saber mais sobre como excluir dados privados em logs de Azure Monitor, consulte [como exportar e excluir dados privados](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data).
