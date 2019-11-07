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
ms.openlocfilehash: 059ca2a26e50128d6bc4313dad9f995e97c06378
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686403"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Gerenciar dados de usuário encontrados em uma investigação da central de segurança do Azure
Este artigo fornece informações sobre como gerenciar os dados de usuário encontrados no recurso de investigação da central de segurança do Azure. Os dados de investigação são armazenados em [logs de Azure monitor](../log-analytics/log-analytics-overview.md) e expostos na central de segurança. O gerenciamento de dados do usuário inclui a capacidade de excluir ou exportar dados.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Pesquisando e identificando dados pessoais
No portal do Azure, você pode usar o [recurso de investigação](../security-center/security-center-investigation.md) da central de segurança para pesquisar dados pessoais. O recurso de investigação está disponível em **alertas de segurança**.

O recurso de investigação mostra todas as entidades, informações de usuário e dados na guia **entidades** .

## <a name="securing-and-controlling-access-to-personal-information"></a>Protegendo e controlando o acesso a informações pessoais
Um usuário da central de segurança atribuiu a função de leitor, proprietário, colaborador ou administrador da conta pode acessar dados do cliente dentro da ferramenta.

Consulte [funções internas para o controle de acesso baseado em função do Azure](../role-based-access-control/built-in-roles.md) para saber mais sobre as funções de leitor, proprietário e colaborador. Consulte [Administradores de assinatura do Azure](../billing/billing-add-change-azure-subscription-administrator.md) para saber mais sobre a função de administrador da conta.

## <a name="deleting-personal-data"></a>Excluindo dados pessoais
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

## <a name="exporting-personal-data"></a>Exportando dados pessoais
Um usuário da central de segurança que atribuiu a função de proprietário, colaborador ou administrador da conta pode exportar as informações de investigação. Para exportar informações de investigação, vá para a guia **entidades** para copiar e colar as informações relevantes.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como gerenciar dados do usuário, consulte [gerenciar dados do usuário na central de segurança do Azure](security-center-privacy.md).
Para saber mais sobre como excluir dados privados em logs de Azure Monitor, consulte [como exportar e excluir dados privados](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data).
