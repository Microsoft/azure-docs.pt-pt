---
title: Obtenha dados de utilização da Máquina Virtual Azure utilizando a API REST
description: Utilize as APIs De REPOUSO Azure para recolher métricas de utilização para uma Máquina Virtual.
author: rloutlaw
ms.service: virtual-machines
ms.subservice: monitoring
ms.custom: REST
ms.topic: article
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 07e91f3d9fd32f01db91415bfd90746cd1aef403
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78944742"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Obtenha métricas de utilização da Máquina Virtual utilizando a API REST

Este exemplo mostra como recuperar o uso do CPU para uma [Máquina Virtual Linux](https://docs.microsoft.com/azure/virtual-machines/linux/monitor) utilizando a [API Bluee REST](/rest/api/azure/).

Documentação completa de referência e amostras adicionais para a API REST estão disponíveis na [referência REST do Monitor Azure](/rest/api/monitor). 

## <a name="build-the-request"></a>Criar o pedido

Utilize o seguinte pedido GET para recolher a [métrica percentual cpu](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftcomputevirtualmachines) de uma máquina virtual

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Cabeçalhos do pedido

Os seguintes cabeçalhos são obrigatórios: 

|Cabeçalho do pedido|Descrição|  
|--------------------|-----------------|  
|*Content-Type:*|Necessário. Definido como `application/json`.|  
|*Authorization:*|Necessário. Definido como um  [token de acesso `Bearer`](/rest/api/azure/#authorization-code-grant-interactive-clients) válido. |  

### <a name="uri-parameters"></a>Parâmetros URI

| Nome | Descrição |
| :--- | :---------- |
| subscriptionId | O ID de subscrição que identifica uma assinatura Azure. Se tiver várias subscrições, consulte [Trabalhar com várias subscrições.](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest) |
| resourceGroupName | O nome do grupo de recursos Azure associado ao recurso. Pode obter este valor a partir do Azure Resource Manager API, CLI ou do portal. |
| vmname | O nome da Máquina Virtual Azure. |
| nomes métricos | Lista separada da vírmeta das [métricas válidas](/azure/load-balancer/load-balancer-standard-diagnostics)do Balancer de Carga . |
| api-version | A versão API a utilizar para o pedido.<br /><br /> Este documento abrange a `2018-01-01`versão api, incluída no URL acima.  |
| timespan | String com o `startDateTime_ISO/endDateTime_ISO` seguinte formato que define a gama de tempo das métricas devolvidas. Este parâmetro opcional está definido para devolver um dia de dados no exemplo. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Corpo do pedido

Não é necessário nenhum corpo de pedido para esta operação.

## <a name="handle-the-response"></a>Processar a resposta

O código de estado 200 é devolvido quando a lista de valores métricos é devolvida com sucesso. Uma lista completa de códigos de erro está disponível na documentação de [referência.](/rest/api/monitor/metrics/list#errorresponse)

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
