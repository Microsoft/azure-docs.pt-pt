---
title: Obter dados de utilização de Máquina Virtual do Azure com a API REST | Documentos da Microsoft
description: Utilize as APIs de REST do Azure para recolher métricas de utilização de uma Máquina Virtual.
services: virtual-machines
author: rloutlaw
ms.reviewer: routlaw
manager: gwallace
ms.service: load-balancer
ms.custom: REST
ms.topic: article
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 53e93d20f4a8c4877374d8242521ca97d481e15d
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667458"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>Obter métrica de utilização de Máquina Virtual com a API REST

Este exemplo mostra como obter a utilização da CPU para um [Máquina Virtual do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/monitor) utilizando o [API REST do Azure](/rest/api/azure/).

Estão disponíveis na documentação de referência completa e exemplos adicionais para a API REST da [referência de REST do Azure Monitor](/rest/api/monitor). 

## <a name="build-the-request"></a>Criar o pedido

Utilize o seguinte pedido GET para recolher os [métrica percentagem de CPU](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftcomputevirtualmachines) de uma Máquina Virtual

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>Cabeçalhos do pedido

Os seguintes cabeçalhos são necessários: 

|Cabeçalho do pedido|Descrição|  
|--------------------|-----------------|  
|*Content-Type:*|Necessário. Definido como `application/json`.|  
|*Authorization:*|Necessário. Definido como válido `Bearer` [token de acesso](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>Parâmetros do URI

| Nome | Descrição |
| :--- | :---------- |
| subscriptionId | O ID de subscrição que identifica uma subscrição do Azure. Se tiver várias subscrições, veja [trabalhar com várias subscrições](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| resourceGroupName | O nome do grupo de recursos do Azure associado com o recurso. Pode obter este valor de API do Gestor de recursos do Azure, CLI ou o portal. |
| vmname | O nome da Máquina Virtual do Azure. |
| metricnames | Lista separada por vírgulas de válido [métricas de Balanceador de carga](/azure/load-balancer/load-balancer-standard-diagnostics). |
| versão de API | A versão de API a utilizar para o pedido.<br /><br /> Este documento abrange a api-version `2018-01-01`, incluído no URL acima.  |
| TimeSpan | Com o seguinte formato de cadeias de caracteres `startDateTime_ISO/endDateTime_ISO` que define o intervalo de tempo das métricas de retornado. Este parâmetro opcional é definido para devolver uma dias de dados no exemplo. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Corpo do pedido

O corpo do pedido não é necessária para esta operação.

## <a name="handle-the-response"></a>Processar a resposta

Código de estado 200 é retornado quando a lista de valores de métrica é devolvida com êxito. Uma lista completa dos códigos de erro está disponível na [documentação de referência](/rest/api/monitor/metrics/list#errorresponse).

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
