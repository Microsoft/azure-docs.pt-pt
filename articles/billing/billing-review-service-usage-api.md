---
title: Reveja a utilização de recursos de serviço do Azure com a REST API | Documentos da Microsoft
description: Saiba como utilizar APIs REST do Azure para rever a utilização de recursos de serviço do Azure.
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
ms.author: erikre
ms.openlocfilehash: d3db4166810da981ff0117536d8550a6b2203924
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370990"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Reveja a utilização de recursos do Azure com a API REST

Ajuda de APIs de gestão de custos do Azure, reveja e gira o consumo de recursos do Azure.

Neste artigo, irá aprender a criar um relatório diário que irá gerar um documento de valores separados por vírgulas com suas informações de utilização por hora e, em seguida, como utilizar filtros para personalizar o relatório para que pode consultar a utilização de máquinas virtuais, bases de dados e etiquetados recursos num grupo de recursos do Azure.

>[!NOTE]
> A API de gestão de custos está atualmente em pré-visualização privada.

## <a name="create-a-basic-cost-management-report"></a>Criar um relatório de gestão de custos básico

Utilize o `reports` operação na API de gestão de custos para definir como os relatórios de custo é gerado e onde os relatórios serão publicados.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

O `{subscriptionGuid}` parâmetro é obrigatório e deve conter um ID de subscrição que pode ser lidos com as credenciais fornecidas no token de API. o `{reportName}`

Os seguintes cabeçalhos são necessários: 

|Cabeçalho do pedido|Descrição|  
|--------------------|-----------------|  
|*Content-Type:*| Necessário. Definido como `application/json`. |  
|*Autorização:*| Necessário. Definido como válido `Bearer` token. |

Configure os parâmetros do relatório no corpo do pedido HTTP. No exemplo abaixo, o relatório está definido para gerar todos os dias, quando é um ficheiro CSV escrito para um contentor de BLOBs de armazenamento do Azure Active Directory e contém informações de custo para todos os recursos no grupo de recursos de hora a hora `westus`.

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

o

## <a name="filtering-reports"></a>Filtragem de relatórios

O `filter` e `dimensions` seção do corpo do pedido durante a criação de um relatório permitem-lhe focam-se nos custos para tipos de recurso específico. O corpo do pedido anterior mostra como filtrar por todos os recursos numa região. 

### <a name="get-all-compute-usage"></a>Obter todas as utilizações de computação

Utilize o `ResourceType` dimensão para comunicar os custos de máquina virtual do Azure na sua subscrição em todas as regiões.

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

### <a name="get-all-database-usage"></a>Obter toda a utilização da base de dados

Utilize o `ResourceType` dimensão para os custos da base de dados do Azure SQL de relatório na sua subscrição em todas as regiões.

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

### <a name="report-on-specific-instances"></a>Relatório sobre instâncias específicas

O `Resource` dimensão permite-lhe comunicar os custos para recursos específicos.

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

Definir o `timeframe` definição para `Custom` para definir um período de tempo fora da semana para a data e mês para as opções incorporadas de data.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>Passos Seguintes
- [Introdução à API REST do Azure](https://docs.microsoft.com/rest/api/azure/)   
