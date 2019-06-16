---
title: Obter eventos de estado de funcionamento com a API REST de recursos do Azure | Documentos da Microsoft
description: Utilize as APIs de REST do Azure para obter os eventos de estado de funcionamento para os seus recursos do Azure.
author: stephbaron
ms.author: stbaron
ms.service: service-health
ms.custom: REST
ms.topic: article
ms.date: 06/06/2017
ms.openlocfilehash: 6d83aed6910127ceb34b9a694f48ca9c19ab6d18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60790917"
---
# <a name="get-resource-health-using-the-rest-api"></a>Obter o estado de funcionamento de recursos com a API REST 

Este artigo de exemplo mostra como recuperar uma lista de eventos de estado de funcionamento dos recursos do Azure na sua subscrição com o [API REST do Azure](/rest/api/azure/).

Estão disponíveis na documentação de referência completa e exemplos adicionais para a API REST da [referência de REST do Azure Monitor](/rest/api/monitor). 

## <a name="build-the-request"></a>Criar o pedido

Utilize o seguinte procedimento `GET` pedido HTTP para listar os eventos de estado de funcionamento para a sua subscrição para o intervalo de tempo entre `2018-05-16` e `2018-06-20`.

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>Cabeçalhos do pedido

Os seguintes cabeçalhos são necessários: 

|Cabeçalho do pedido|Descrição|  
|--------------------|-----------------|  
|*Content-Type:*|Necessário. Definido como `application/json`.|  
|*Authorization:*|Necessário. Definido como válido `Bearer` [token de acesso](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>Parâmetros do URI

| Name | Descrição |
| :--- | :---------- |
| subscriptionId | O ID de subscrição que identifica uma subscrição do Azure. Se tiver várias subscrições, veja [trabalhar com várias subscrições](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| versão de API | A versão de API a utilizar para o pedido.<br /><br /> Este documento abrange a api-version `2015-04-01`, incluído no URL acima.  |
| $filter | A opção de filtragem para reduzir o conjunto de resultados devolvidos. Os padrões permitidos para este parâmetro estão disponíveis [de referência para a operação de registos de atividades](/rest/api/monitor/activitylogs/list#uri-parameters). O exemplo mostrado a captura todos os eventos num intervalo de tempo entre 2018-05-16 e de 2018-06-20 |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Corpo do pedido

O corpo do pedido não é necessária para esta operação.

## <a name="handle-the-response"></a>Processar a resposta

Código de estado 200 é devolvido com uma lista de valores de eventos de estado de funcionamento correspondente para o parâmetro filter, juntamente com um `nextlink` URI para obter a seguinte página de resultados.

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
