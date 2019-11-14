---
title: Obter dados de uso da máquina virtual do Azure usando a API REST
description: Use as APIs REST do Azure para coletar métricas de utilização para uma máquina virtual.
services: virtual-machines
author: rloutlaw
ms.reviewer: routlaw
manager: gwallace
ms.service: load-balancer
ms.custom: REST
ms.topic: article
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 523b81e53f2b0622b237993dbd88fb9492079c86
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035816"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Obter métricas de uso da máquina virtual usando a API REST

Este exemplo mostra como recuperar o uso da CPU para uma [máquina virtual do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/monitor) usando a [API REST do Azure](/rest/api/azure/).

A documentação de referência completa e exemplos adicionais para a API REST estão disponíveis na [referência REST do Azure monitor](/rest/api/monitor). 

## <a name="build-the-request"></a>Criar o pedido

Use a seguinte solicitação GET para coletar a [métrica percentual de CPU](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftcomputevirtualmachines) de uma máquina virtual

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
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
| resourceGroupName | O nome do grupo de recursos do Azure associado ao recurso. Você pode obter esse valor da API Azure Resource Manager, da CLI ou do Portal. |
| vmname | O nome da máquina virtual do Azure. |
| metricnames | Lista separada por vírgulas de [métricas de Load Balancer](/azure/load-balancer/load-balancer-standard-diagnostics)válidas. |
| api-version | A versão da API a ser usada para a solicitação.<br /><br /> Este documento aborda a versão de API `2018-01-01`, incluída na URL acima.  |
| TimeSpan | Cadeia de caracteres com o seguinte formato `startDateTime_ISO/endDateTime_ISO` que define o intervalo de tempo das métricas retornadas. Esse parâmetro opcional é definido para retornar o valor de dados de um dia no exemplo. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Corpo do pedido

Nenhum corpo de solicitação é necessário para esta operação.

## <a name="handle-the-response"></a>Processar a resposta

O código de status 200 é retornado quando a lista de valores de métrica é retornada com êxito. Uma lista completa de códigos de erro está disponível na [documentação de referência](/rest/api/monitor/metrics/list#errorresponse).

## <a name="example-response"></a>Resposta de exemplo 

```json
{
    "cost": 0,
    "timespan": "2018-06-08T23:48:10Z/2018-06-09T00:48:10Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "Percentage CPU",
                "localizedValue": "Percentage CPU"
            },
            "unit": "Percent",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-08T23:48:00Z",
                            "average": 0.44
                        },
                        {
                            "timeStamp": "2018-06-08T23:49:00Z",
                            "average": 0.31
                        },
                        {
                            "timeStamp": "2018-06-08T23:50:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:51:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:52:00Z",
                            "average": 0.285
                        } ]
                } ]
        } ]
}
```
