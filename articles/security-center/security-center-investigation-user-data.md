---
title: Gerir os dados dos utilizadores encontrados numa investigação do Azure Security Center
description: " Saiba como gerir os dados dos utilizadores encontrados na funcionalidade de investigação do Azure Security Center. "
services: operations-management-suite
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: memildin
ms.openlocfilehash: 88fd707d769c7aed53160a9f22fefd15cce19a4b
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340738"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Gerir os dados dos utilizadores encontrados numa investigação do Azure Security Center
Este artigo fornece informações sobre como gerir os dados do utilizador encontrados na funcionalidade de investigação do Azure Security Center. Os dados da investigação são armazenados em [registos do Monitor Azure](../azure-monitor/log-query/log-query-overview.md) e expostos no Centro de Segurança. A gestão dos dados dos utilizadores inclui a capacidade de eliminar ou exportar dados.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Procurar e identificar dados pessoais
No portal Azure, pode utilizar a funcionalidade de investigação do Security Center para procurar dados pessoais. O recurso de investigação está disponível em **Alertas de Segurança.**

A funcionalidade de investigação mostra todas as entidades, informações do utilizador e dados no separador **Entidades.**

## <a name="securing-and-controlling-access-to-personal-information"></a>Proteger e controlar o acesso a informações pessoais
Um utilizador do Security Center atribuiu o papel de Leitor, Proprietário, Colaborador ou Administrador de Conta pode aceder aos dados do cliente dentro da ferramenta.

Consulte [as funções incorporadas para o controlo de acesso baseado em funções Azure](../role-based-access-control/built-in-roles.md) para saber mais sobre as funções de Leitor, Proprietário e Contribuinte. Consulte [os administradores de subscrição do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md) para saber mais sobre a função de Administrador de Conta.

## <a name="deleting-personal-data"></a>Eliminar dados pessoais
Um utilizador do Centro de Segurança atribuiu o papel de Proprietário, Colaborador ou Administrador de Conta pode apagar as informações de investigação.

Para eliminar uma investigação, pode submeter um `DELETE` pedido à API do Gestor de Recursos Azure:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

A `incidentName` entrada pode ser encontrada listando todos os incidentes usando um `GET` pedido:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Exportar dados pessoais
Um utilizador do Centro de Segurança atribuiu o papel de Proprietário, Contribuinte ou Administrador de Conta pode exportar as informações de investigação. Para exportar informações sobre investigação, vá ao separador **Entidades** para copiar e colar as informações relevantes.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a gestão dos dados dos utilizadores, consulte [Gerir os dados do utilizador no Azure Security Center](security-center-privacy.md).
Para saber mais sobre a eliminação de dados privados nos registos do Azure Monitor, consulte [Como exportar e eliminar dados privados.](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data)