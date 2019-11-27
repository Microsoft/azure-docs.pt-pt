---
title: Recuperar métricas com a API REST
titleSuffix: Azure Load Balancer
description: Neste artigo, comece a usar as APIs REST do Azure para coletar métricas de integridade e uso para Azure Load Balancer.
services: sql-database
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 760ec8a945ab88b63dde2de75f5354818facf4f2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225245"
---
# <a name="get-load-balancer-usage-metrics-using-the-rest-api"></a>Obter Load Balancer métricas de uso usando a API REST

Coletar o número de bytes processados por um [Standard Load Balancer](/azure/load-balancer/load-balancer-standard-overview) para um intervalo de tempo usando a [API REST do Azure](/rest/api/azure/).

A documentação de referência completa e exemplos adicionais para a API REST estão disponíveis na [referência REST do Azure monitor](/rest/api/monitor). 

## <a name="build-the-request"></a>Criar o pedido

Use a seguinte solicitação GET para coletar a [métrica byteCount](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics) de um Standard Load Balancer. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Cabeçalhos do pedido

Os seguintes cabeçalhos são obrigatórios: 

|Cabeçalho do pedido|Descrição|  
|--------------------|-----------------|  
|*Content-Type:*|Necessário. Definido como `application/json`.|  
|*Authorization:*|Necessário. Definido com um `Bearer` [token de acesso](/rest/api/azure/#authorization-code-grant-interactive-clients) válido. |  

### <a name="uri-parameters"></a>Parâmetros do URI

| Nome | Descrição |
| :--- | :---------- |
| subscriptionId | A ID da assinatura que identifica uma assinatura do Azure. Se você tiver várias assinaturas, consulte [trabalhando com várias assinaturas](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | O nome do grupo de recursos que contém o recurso. Você pode obter esse valor da API Azure Resource Manager, da CLI ou do Portal. |
| loadBalancerName | O nome do Azure Load Balancer. |
| nomes de métrica | Lista separada por vírgulas de [métricas de Load Balancer](/azure/load-balancer/load-balancer-standard-diagnostics)válidas. |
| api-version | A versão da API a ser usada para a solicitação.<br /><br /> Este documento aborda a versão de API `2018-01-01`, incluída na URL acima.  |
| TimeSpan | O TimeSpan da consulta. É uma cadeia de caracteres com o seguinte formato `startDateTime_ISO/endDateTime_ISO`. Esse parâmetro opcional é definido para retornar o valor de dados de um dia no exemplo. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Corpo do pedido

Nenhum corpo de solicitação é necessário para esta operação.

## <a name="handle-the-response"></a>Processar a resposta

O código de status 200 é retornado quando a lista de valores de métrica é retornada com êxito. Uma lista completa de códigos de erro está disponível na [documentação de referência](/rest/api/monitor/metrics/list#errorresponse).

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
