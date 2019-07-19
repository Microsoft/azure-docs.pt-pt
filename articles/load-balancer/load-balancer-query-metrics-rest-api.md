---
title: Recuperar métricas com a API REST
titlesuffix: Azure Load Balancer
description: Use as APIs REST do Azure para coletar métricas de integridade e utilização para Load Balancer para um determinado intervalo de tempo e datas.
services: sql-database
author: asudbring
ms.reviewer: routlaw
manager: jeconnoc
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 06/06/2017
ms.author: allensu
ms.openlocfilehash: 0d12dc04aff58dd6273d8d29d422bdbd9e7c886b
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274530"
---
# <a name="get-load-balancer-utilization-metrics-using-the-rest-api"></a>Obter Load Balancer métricas de utilização usando a API REST

Este "como" mostra como coletar o número de bytes processados por um [Standard Load Balancer](/azure/load-balancer/load-balancer-standard-overview) para um intervalo de tempo usando a [API REST do Azure](/rest/api/azure/).

A documentação de referência completa e exemplos adicionais para a API REST estão disponíveis na [referência REST do Azure monitor](/rest/api/monitor). 

## <a name="build-the-request"></a>Criar o pedido

Use a seguinte solicitação GET para coletar a [métrica byteCount](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics) de um Standard Load Balancer. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Cabeçalhos do pedido

Os seguintes cabeçalhos são necessários: 

|Cabeçalho da solicitação|Descrição|  
|--------------------|-----------------|  
|*Tipo de conteúdo:*|Necessário. Defina como `application/json`.|  
|*Authorization:*|Necessário. Defina como um token `Bearer` de [acesso](/rest/api/azure/#authorization-code-grant-interactive-clients)válido. |  

### <a name="uri-parameters"></a>Parâmetros do URI

| Nome | Descrição |
| :--- | :---------- |
| subscriptionId | A ID da assinatura que identifica uma assinatura do Azure. Se você tiver várias assinaturas, consulte [trabalhando com várias assinaturas](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | O nome do grupo de recursos que contém o recurso. Você pode obter esse valor da API Azure Resource Manager, da CLI ou do Portal. |
| loadBalancerName | O nome do Azure Load Balancer. |
| metricnames | Lista separada por vírgulas de [métricas de Load Balancer](/azure/load-balancer/load-balancer-standard-diagnostics)válidas. |
| versão de API | A versão da API a ser usada para a solicitação.<br /><br /> Este documento aborda a versão `2018-01-01`de API, incluída na URL acima.  |
| TimeSpan | O TimeSpan da consulta. É uma cadeia de caracteres com o formato `startDateTime_ISO/endDateTime_ISO`a seguir. Esse parâmetro opcional é definido para retornar o valor de dados de um dia no exemplo. |
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
