---
title: Obtenha eventos de saúde de recursos Azure usando a API REST  Microsoft Docs
description: Utilize as APIs Azure REST para obter os eventos de saúde para os seus recursos Azure.
ms.topic: conceptual
ms.date: 06/06/2017
ms.openlocfilehash: 6964a6c4e85c38d532b12e730a02c4df73be76e5
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77654006"
---
# <a name="get-resource-health-using-the-rest-api"></a>Obtenha saúde de recursos usando a API REST 

Este artigo de exemplo mostra como recuperar uma lista de eventos de saúde para os recursos Azure na sua subscrição usando a [API Do REST Azure](/rest/api/azure/).

Documentação completa de referência e amostras adicionais para a API REST estão disponíveis na [referência REST do Monitor Azure](/rest/api/monitor). 

## <a name="build-the-request"></a>Criar o pedido

Utilize o seguinte pedido `GET` HTTP para listar os eventos de saúde para a sua subscrição durante o intervalo de tempo entre `2018-05-16` e `2018-06-20`.

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>Cabeçalhos do pedido

Os seguintes cabeçalhos são obrigatórios: 

|Cabeçalho do pedido|Descrição|  
|--------------------|-----------------|  
|*Content-Type:*|Necessário. Definido como `application/json`.|  
|*Authorization:*|Necessário. Definido como um `Bearer`token de acesso [ ](/rest/api/azure/#authorization-code-grant-interactive-clients) válido. |  

### <a name="uri-parameters"></a>Parâmetros do URI

| Nome | Descrição |
| :--- | :---------- |
| subscriptionId | O ID de subscrição que identifica uma assinatura Azure. Se tiver várias subscrições, consulte [Trabalhar com várias subscrições.](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest) |
| api-version | A versão API a utilizar para o pedido.<br /><br /> Este documento abrange `2015-04-01`de versão api, incluído sem elásga.  |
| $filter | A opção de filtragem para reduzir o conjunto de resultados devolvidos. Os padrões admissíveis para este parâmetro estão disponíveis [na referência para a operação Registos de Atividade](/rest/api/monitor/activitylogs/list#uri-parameters). O exemplo mostrado captura todos os eventos num intervalo de tempo entre 2018-05-16 e 2018-06-20 |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Corpo do pedido

Não é necessário nenhum corpo de pedido para esta operação.

## <a name="handle-the-response"></a>Processar a resposta

O código de estado 200 é devolvido com uma lista de valores de eventos de saúde correspondentes ao parâmetro do filtro, juntamente com um `nextlink` URI para recuperar a próxima página dos resultados.

## <a name="example-response"></a>Resposta de exemplo 

```json
{
  "value": [
    {
      "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
      "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
      },
      "id": "/subscriptions/{subscription-id}/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
      "resourceGroupName": "MSSupportGroup",
      "resourceProviderName": {
        "value": "microsoft.support",
        "localizedValue": "microsoft.support"
      },
      "operationName": {
        "value": "microsoft.support/supporttickets/write",
        "localizedValue": "microsoft.support/supporttickets/write"
      },
      "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
      },
      "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
      "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
      "level": "Informational"
    }
  ],
  "nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```
