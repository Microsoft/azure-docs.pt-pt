---
title: Gerir os dados de utilizador encontrados numa investigação do Centro de segurança do Azure | Documentos da Microsoft
description: " Saiba como gerir os dados de utilizador encontrados na funcionalidade de investigação do Centro de segurança do Azure. "
services: operations-management-suite
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: operations-management-suite
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: rkarlin
ms.openlocfilehash: bec9be627937913c268d65bb6cdadcf68c7a3fb4
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244303"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Gerir os dados de utilizador encontrados numa investigação do Centro de segurança do Azure
Este artigo fornece informações sobre como gerir os dados de utilizador encontrados na funcionalidade de investigação do Centro de segurança do Azure. Investigação de dados é armazenada no [registos do Azure Monitor](../log-analytics/log-analytics-overview.md) e expostos no Centro de segurança. Gestão de dados de utilizador inclui a capacidade de eliminar ou exportar dados.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>A procurar e identificar os dados pessoais
No portal do Azure, pode utilizar o Centro de segurança [funcionalidade de investigação](../security-center/security-center-investigation.md) para procurar os dados pessoais. A funcionalidade de investigação está disponível em **alertas de segurança**.

A funcionalidade de investigação mostra todas as entidades, informações de utilizador e dados sob o **entidades** separador.

## <a name="securing-and-controlling-access-to-personal-information"></a>Protegendo e controlando o acesso a informações pessoais
Um utilizador do Centro de segurança atribuída a função de leitor, proprietário, Contribuidor ou administrador de conta pode aceder a dados do cliente dentro da ferramenta.

Ver [funções incorporadas para controlo de acesso baseado em funções do Azure](../role-based-access-control/built-in-roles.md) para saber mais sobre as funções do leitor e contribuinte proprietário. Ver [os administradores de subscrição do Azure](../billing/billing-add-change-azure-subscription-administrator.md) para saber mais sobre a função de administrador de conta.

## <a name="deleting-personal-data"></a>A eliminar os dados pessoais
A função de proprietário, contribuinte, atribuída a um utilizador de centro de segurança ou administrador de conta pode eliminar as informações de investigação.

Para eliminar uma investigação, pode submeter um `DELETE` pedido para a API de REST do Azure Resource Manager:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

O `incidentName` entrada pode ser encontrada listando todos os incidentes utilizando um `GET` pedido:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Exportar os dados pessoais
A função de proprietário, contribuinte, atribuída a um utilizador de centro de segurança ou administrador de conta pode exportar as informações de investigação. Para exportar as informações de investigação, vá para o **entidades** separador para copiar e colar as informações relevantes.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre a gestão de dados do utilizador, consulte [gerir os dados de utilizador no Centro de segurança do Azure](security-center-privacy.md).
Para saber mais sobre a eliminação de dados privados nos registos do Azure Monitor, consulte [como exportar e eliminar dados privados](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data).
