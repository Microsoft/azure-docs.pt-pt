---
title: Aposentadoria Azure Monitor API
description: Descreve a reforma de versões mais antigas da API fornecedora de recursos OperationalInsights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/29/2020
ms.openlocfilehash: 4075e27c3711cbc4c584005126b53f7c99942049
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926730"
---
# <a name="operationalinsights-api-version-retirement"></a>OperacionalInsights API versão reforma
A Microsoft fornece a notificação com pelo menos 12 meses de antecedência para retirar uma API de forma a suavizar a transição para uma versão mais recente/suportada. Lançámos uma nova versão (2020-08-01) para o fornecedor de recursos **OperationalInsights** APIs e reformaremos quaisquer versões anteriores da API em 29 de fevereiro de 2014.

Encorajamo-lo a começar a usar a versão 2020-08-01 agora para obter os benefícios de novas funcionalidades, tais como [cluster dedicado,](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters) [chaves geridas pelo cliente,](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys) [link privado](https://docs.microsoft.com/azure/azure-monitor/platform/private-link-security) e exportação de [dados.](https://docs.microsoft.com/azure/azure-monitor/platform/logs-data-export) Além disso, novas funcionalidades e funcionalidades e otimizações são adicionadas apenas à API atual.

Depois de 29 de fevereiro de 2014, o Azure Monitor deixará de suportar versões APIs anteriores do que 2020-08-01. Se preferir não fazer upgrade, os pedidos enviados de versões anteriores continuarão a ser servidos pelo serviço Azure Monitor até 29 de fevereiro de 2014.

## <a name="migration-steps"></a>Passos de migração
Dependendo do método de configuração que utiliza, deverá atualizar a nova versão em pedidos **de REST** e **modelos de Gestor de Recursos** . Siga os exemplos abaixo para atualizar a versão API:

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

- Consulte a [referência para o espaço de trabalho OperationalInsights API](/rest/api/loganalytics/workspaces).
