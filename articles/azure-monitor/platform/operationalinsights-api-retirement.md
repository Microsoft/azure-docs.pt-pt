---
title: Aposentadoria Azure Monitor API
description: Descreve a reforma de versões mais antigas da API fornecedora de recursos OperationalInsights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: 708901dbc37daa075cf38d3f9ef046ae658ea979
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91744893"
---
# <a name="operationalinsights-api-version-retirement"></a>OperacionalInsights API versão reforma
A Microsoft fornece a notificação com pelo menos 12 meses de antecedência para retirar uma API de forma a suavizar a transição para uma versão mais recente/suportada. Lançámos uma nova versão (2020-08-01) para o fornecedor de recursos **OperationalInsights** APIs e reformaremos quaisquer versões anteriores da API a 31 de outubro de 2023. Uma vez que novas funcionalidades e funcionalidades e otimizações só são adicionadas à API atual, deve fazer o upgrade para a versão API mais recente o mais cedo possível.

Depois de 31 de outubro de 2023, o Azure Monitor deixará de suportar versões APIs anteriores do que 2020-08-01. Se preferir não fazer upgrade, os pedidos enviados de versões anteriores continuarão a ser servidos pelo serviço Azure Monitor até 31 de outubro de 2023.

Dependendo do método de configuração que utiliza, deverá atualizar a nova versão em pedidos DE REST e modelos de Gestor de Recursos de acordo com os seguintes exemplos:


## <a name="rest"></a>REST
Rest API solicita utilizar a versão API no URL do pedido. Substitua esta versão pela versão mais recente, como mostra o exemplo a seguir.

```rest
https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
```

## <a name="azure-resource-manager"></a>Azure Resource Manager
Os modelos do Gestor de Recursos utilizam a versão API na propriedade **apiversion do** recurso. Substitua esta versão pela versão mais recente, como mostra o exemplo a seguir.


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
              "description": "Name of the workspace."
            }
        },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2020-08-01",
            "location": "westus",
            "properties": {
                "sku": {
                    "name": "pergb2018"
                },
                "retentionInDays": 30,
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            }
        }
    ]
  }
}
```


## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência para a API OperacionalInsights](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/allversions).
