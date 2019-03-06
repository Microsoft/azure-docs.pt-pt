---
title: Obter métricas com a API REST
titlesuffix: Azure Load Balancer
description: Utilize as APIs de REST do Azure para recolher métricas de utilização e estado de funcionamento do Balanceador de carga para um determinado intervalo de datas e horários.
services: sql-database
author: KumudD
ms.reviewer: routlaw
manager: jeconnoc
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 06/06/2017
ms.author: KumudD
ms.openlocfilehash: 0d8ac22679bc4e789c22396c21c51dacee201302
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433846"
---
# <a name="get-load-balancer-utilization-metrics-using-the-rest-api"></a>Obter métricas de utilização de Balanceador de carga com a API REST

Nesta explicação de procedimento mostra como coletar o número de bytes processados por um [Balanceador de carga Standard](/azure/load-balancer/load-balancer-standard-overview) para um intervalo de tempo utilizando o [API REST do Azure](/rest/api/azure/).

Estão disponíveis na documentação de referência completa e exemplos adicionais para a API REST da [referência de REST do Azure Monitor](/rest/api/monitor). 

## <a name="build-the-request"></a>Criar o pedido

Utilize o seguinte pedido GET para recolher os [métrica ByteCount](/azure/load-balancer/load-balancer-standard-diagnostics#a-name--multidimensionalmetricsamulti-dimensional-metrics) de um balanceador de carga Standard. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Cabeçalhos do pedido

Os seguintes cabeçalhos são necessários: 

|Cabeçalho do pedido|Descrição|  
|--------------------|-----------------|  
|*Content-Type:*|Necessário. Definido como `application/json`.|  
|*Autorização:*|Necessário. Definido como válido `Bearer` [token de acesso](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>Parâmetros do URI

| Name | Descrição |
| :--- | :---------- |
| subscriptionId | O ID de subscrição que identifica uma subscrição do Azure. Se tiver várias subscrições, veja [trabalhar com várias subscrições](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | O nome do grupo de recursos que contém o recurso. Pode obter este valor de API do Gestor de recursos do Azure, CLI ou o portal. |
| loadBalancerName | O nome do Balanceador de carga do Azure. |
| metricnames | Lista separada por vírgulas de válido [métricas de Balanceador de carga](/azure/load-balancer/load-balancer-standard-diagnostics). |
| versão de API | A versão de API a utilizar para o pedido.<br /><br /> Este documento abrange a api-version `2018-01-01`, incluído no URL acima.  |
| Período de tempo | O período de tempo da consulta. É uma cadeia de caracteres com o seguinte formato `startDateTime_ISO/endDateTime_ISO`. Este parâmetro opcional é definido para devolver uma dias de dados no exemplo. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Corpo do pedido

O corpo do pedido não é necessária para esta operação.

## <a name="handle-the-response"></a>Processar a resposta

Código de estado 200 é retornado quando a lista de valores de métrica é devolvida com êxito. Uma lista completa dos códigos de erro está disponível na [documentação de referência](/rest/api/monitor/metrics/list#errorresponse).

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
