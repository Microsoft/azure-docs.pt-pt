---
title: Recuperar métricas com a API REST
titleSuffix: Azure Load Balancer
description: Neste artigo, começa a utilizar as APIs do Azure REST para recolher métricas de saúde e utilização para o Azure Load Balancer.
services: sql-database
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: how-to
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 3b5aedb20bc7a8d2aa6f3aa3d8691a71af4cd3a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84808378"
---
# <a name="get-load-balancer-usage-metrics-using-the-rest-api"></a>Obtenha métricas de utilização do Balanceador de Carga utilizando a API REST

Recolher o número de bytes processados por um [Balanceador de Carga Padrão](/azure/load-balancer/load-balancer-standard-overview) por um intervalo de tempo utilizando a [API Azure REST](/rest/api/azure/).

A documentação completa de referência e amostras adicionais para a API REST estão disponíveis na [referência Azure Monitor REST](/rest/api/monitor). 

## <a name="build-the-request"></a>Criar o pedido

Utilize o seguinte pedido GET para recolher a [métrica ByteCount](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics) de um Balanceador de Carga Padrão. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Cabeçalhos do pedido

Os seguintes cabeçalhos são obrigatórios: 

|Cabeçalho do pedido|Descrição|  
|--------------------|-----------------|  
|*Content-Type:*|Necessário. Definido como `application/json`.|  
|*Authorization:*|Necessário. Definido como um  [token de acesso `Bearer`](/rest/api/azure/#authorization-code-grant-interactive-clients) válido. |  

### <a name="uri-parameters"></a>Parâmetros URI

| Name | Descrição |
| :--- | :---------- |
| subscriptionId | O ID de subscrição que identifica uma subscrição do Azure. Se tiver várias subscrições, consulte [Trabalhar com várias subscrições](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | O nome do grupo de recursos que contém o recurso. Pode obter este valor a partir da Azure Resource Manager API, CLI ou do portal. |
| loadBalancerName | O nome do Equilibrador de Carga Azure. |
| nomes métricos | Lista separada por vírgulas de [métricas válidas do Balanceador](/azure/load-balancer/load-balancer-standard-diagnostics)de Carga . |
| api-version | A versão API a utilizar para o pedido.<br /><br /> Este documento abrange a versão `2018-01-01` api, incluída no URL acima.  |
| timespan | O tempo da consulta. É uma corda com o seguinte `startDateTime_ISO/endDateTime_ISO` formato. Este parâmetro opcional está definido para devolver um dia de dados no exemplo. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Corpo do pedido

Não é necessário nenhum corpo de pedido para esta operação.

## <a name="handle-the-response"></a>Processar a resposta

O código de estado 200 é devolvido quando a lista de valores métricos é devolvida com sucesso. Uma lista completa de códigos de erro está disponível na [documentação de referência.](/rest/api/monitor/metrics/list#errorresponse)

## <a name="example-response"></a>Resposta de exemplo 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```
