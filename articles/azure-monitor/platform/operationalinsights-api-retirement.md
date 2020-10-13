---
title: Aposentadoria Azure Monitor API
description: Descreve a reforma de versões mais antigas da API fornecedora de recursos OperationalInsights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: cf48e26133326d43754b38df6f3b2caaf7a587ab
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978093"
---
# <a name="operationalinsights-api-version-retirement"></a>OperacionalInsights API versão reforma
A Microsoft fornece a notificação com pelo menos 12 meses de antecedência para retirar uma API de forma a suavizar a transição para uma versão mais recente/suportada. Lançámos uma nova versão (2020-08-01) para o fornecedor de recursos **OperationalInsights** APIs e reformaremos quaisquer versões anteriores da API a 31 de outubro de 2023. Uma vez que novas funcionalidades e funcionalidades e otimizações só são adicionadas à API atual, deve fazer o upgrade para a versão API mais recente o mais cedo possível.

Depois de 31 de outubro de 2023, o Azure Monitor deixará de suportar versões APIs anteriores do que 2020-08-01. Se preferir não fazer upgrade, os pedidos enviados de versões anteriores continuarão a ser servidos pelo serviço Azure Monitor até 31 de outubro de 2023.

## <a name="migration-steps"></a>Passos de migração
Dependendo do método de configuração que utiliza, deverá atualizar a nova versão em pedidos **de REST** e **modelos de Gestor de Recursos**. Siga os exemplos abaixo para atualizar a versão API:

1. Rest API solicita utilizar a versão API no URL do pedido. Substitua esta versão pela versão mais recente (2020-08-01), como mostra o exemplo seguinte.

    ```rest
    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
    ```

2. Os modelos Azure Resource Manager utilizam a versão API na propriedade **apiversion do** recurso. Substitua esta versão pela versão mais recente (2020-08-01), como mostra o exemplo seguinte.


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
