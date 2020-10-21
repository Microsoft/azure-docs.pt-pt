---
title: Analisar a utilização dos recursos do serviço do Azure com a API REST
description: Saiba como utilizar as APIs REST do Azure para analisar a utilização dos recursos do serviço do Azure. Crie um relatório de gestão de custos e filtre por tipos de recursos específicos.
author: lleonard-msft
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: reference
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 018e61fbc74091fa25211b8a25ce067862a0cc7d
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132402"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Analisar a utilização dos recursos do Azure com a API REST

As APIs do Azure Cost Management ajudam-no a analisar e a gerir o consumo dos recursos do Azure.

Neste artigo, ficará a saber como criar um relatório diário para gerar um documento de valores separados por vírgulas com as informações de utilização por hora e, em seguida, como utilizar filtros para personalizar o relatório para que possa consultar a utilização das máquinas virtuais, das bases de dados e dos recursos etiquetados num grupo de recursos do Azure.

>[!NOTE]
> A API do Cost Management está atualmente em pré-visualização privada.

## <a name="create-a-basic-cost-management-report"></a>Criar um relatório de gestão de custos básico

Utilize a operação `reports` na API do Cost Management para definir como o relatório de custos é gerado e onde os relatórios serão publicados.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

O parâmetro `{subscriptionGuid}` é obrigatório e deve conter um ID de subscrição que pode ser lido com as credenciais fornecidas no token da API. 

O parâmetro `{reportName}` especifica o nome do relatório. Para obter a lista dos nomes de relatórios, pode utilizar a operação Reports_List: `/subscriptions/{subscriptionId}/providers/Microsoft.CostManagement/reports`. Veja a saída de exemplo no [GitHub](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/cost-management/resource-manager/Microsoft.CostManagement/preview/2018-08-01-preview/examples/ReportList.json).

Os seguintes cabeçalhos são obrigatórios:

|Cabeçalho do pedido|Descrição|  
|--------------------|-----------------|  
|*Content-Type:*| Necessário. Definido como `application/json`. |  
|*Authorization:*| Necessário. Definido como um token `Bearer` válido. |

Configure os parâmetros do relatório no corpo do pedido HTTP. No exemplo abaixo, o relatório está definido para ser gerado diariamente quando ativo. Trata-se de um ficheiro CSV escrito para um contentor de blobs do Armazenamento do Microsoft Azure e contém informações de custos por hora para todos os recursos num grupo de recursos `westus`.

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

## <a name="filtering-reports"></a>Filtrar relatórios

As secções `filter` e `dimensions` do corpo do pedido ao criar um relatório permitem que se concentre nos custos de tipos de recursos específicos. O corpo do pedido anterior mostra como filtrar por todos os recursos numa região.

### <a name="get-all-compute-usage"></a>Obter toda a utilização de computação

Utilize a dimensão `ResourceType` para comunicar os custos da máquina virtual do Azure na sua subscrição em todas as regiões.

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

### <a name="get-all-database-usage"></a>Obter toda a utilização das bases de dados

Utilize a dimensão `ResourceType` para comunicar os custos da Base de Dados SQL do Azure na sua subscrição em todas as regiões.

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

### <a name="report-on-specific-instances"></a>Comunicar instâncias específicas

A dimensão `Resource` permite-lhe comunicar os custos de recursos específicos.

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

### <a name="changing-timeframes"></a>Alterar os períodos de tempo

Configure a definição `timeframe` para `Custom` para definir um período de tempo fora das opções incorporadas da semana até à data e do mês até à data.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>Passos seguintes
- [Introdução à API REST do Azure](/rest/api/azure/)