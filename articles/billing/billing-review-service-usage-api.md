---
title: Examinar o uso de recursos do serviço do Azure com a API REST | Microsoft Docs
description: Saiba como usar as APIs REST do Azure para examinar o uso de recursos de serviço do Azure.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: banders
ms.openlocfilehash: 47e19fae26d6e3bc465799980c587d7bb7ed5e92
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443071"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Examinar o uso de recursos do Azure usando a API REST

As APIs de gerenciamento de custos do Azure ajudam você a revisar e gerenciar o consumo de seus recursos do Azure.

Neste artigo, você aprenderá a criar um relatório diário que irá gerar um documento de valor separado por vírgula com suas informações de uso por hora e como usar filtros para personalizar o relatório para que você possa consultar o uso de máquinas virtuais, bancos de dados e marcas recursos em um grupo de recursos do Azure.

>[!NOTE]
> A API de gerenciamento de custos está atualmente em visualização privada.

## <a name="create-a-basic-cost-management-report"></a>Criar um relatório de gerenciamento de custo básico

Use a `reports` operação na API de gerenciamento de custos para definir como o relatório de custos é gerado e para onde os relatórios serão publicados.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

O `{subscriptionGuid}` parâmetro é necessário e deve conter uma ID de assinatura que pode ser lida usando as credenciais fornecidas no token da API. Dos`{reportName}`

Os seguintes cabeçalhos são necessários: 

|Cabeçalho da solicitação|Descrição|  
|--------------------|-----------------|  
|*Tipo de conteúdo:*| Necessário. Defina como `application/json`. |  
|*Authorization:*| Necessário. Defina como um token `Bearer` válido. |

Configure os parâmetros do relatório no corpo da solicitação HTTP. No exemplo a seguir, o relatório é definido para gerar todos os dias quando ativo, é um arquivo CSV gravado em um contêiner de blobs de armazenamento do Azure e contém informações de custo por hora para `westus`todos os recursos no grupo de recursos.

```json
{
    "properties": {
        "schedule": {
            "status": "Inactive",
            "recurrence": "Daily",
            "recurrencePeriod": {
                "from": "2018-08-21",
                "to": "2019-10-31"
            }
        },
        "deliveryInfo": {
            "destination": {
                "resourceId": "/subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
                "container": "MyReportContainer",
                "rootFolderPath": "MyScheduleTest"
            }
        },
        "format": "Csv",
        "definition": {
            "type": "Usage",
            "timeframe": "MonthToDate",
            "dataSet": {
                "granularity": "Hourly",
                "filter": {
                    "dimensions": {
                        "name": "ResourceLocation",
                        "operator": "In",
                        "values": [
                            "westus"
                        ]
                    }
                }
            }
        }
    }
}
```

O

## <a name="filtering-reports"></a>Filtrando relatórios

A `filter` seção `dimensions` e do corpo da solicitação ao criar um relatório permite que você se concentre nos custos de tipos de recursos específicos. O corpo da solicitação anterior mostra como filtrar por todos os recursos em uma região. 

### <a name="get-all-compute-usage"></a>Obter todo o uso de computação

Use a `ResourceType` dimensão para relatar os custos da máquina virtual do Azure em sua assinatura em todas as regiões.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.ClassicCompute/virtualMachines", 
                "Microsoft.Compute/virtualMachines"
        ] 
    }
}
```

### <a name="get-all-database-usage"></a>Obter todo o uso do banco de dados

Use a `ResourceType` dimensão para relatar os custos do banco de dados SQL do Azure em sua assinatura em todas as regiões.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.Sql/servers"
        ] 
    }
}
```

### <a name="report-on-specific-instances"></a>Relatório em instâncias específicas

A `Resource` dimensão permite que você relate os custos para recursos específicos.

```json
"filter": {
    "dimensions": {
        "name": "Resource",
        "operator": "In",
        "values": [
            "subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroup}/providers/Microsoft.ClassicCompute/virtualMachines/{ResourceName}"
        ]
    }
}
```

### <a name="changing-timeframes"></a>Alterando os períodos de tempo

Defina a `timeframe` definição como `Custom` para definir um período de tempo fora das opções da semana até a data e do mês até a data interna.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>Passos Seguintes
- [Introdução à API REST do Azure](https://docs.microsoft.com/rest/api/azure/)   
