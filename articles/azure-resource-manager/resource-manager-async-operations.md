---
title: Status de operações assíncronas
description: Descreve como monitorizar operações assíncronas no Azure. Mostra os valores que utilizar para obter o estado de uma operação de longa execução.
ms.topic: conceptual
ms.date: 12/09/2018
ms.custom: seodec18
ms.openlocfilehash: 39174010e5d5c71e1b9f09827f92a2c540d98e78
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149533"
---
# <a name="track-asynchronous-azure-operations"></a>Monitorizar operações assíncronas do Azure
Algumas operações de REST de Azure executam de maneira assíncrona porque não é possível concluir a operação rapidamente. Este artigo descreve como controlar o estado das operações assíncronas por meio de valores devolvidos na resposta.  

## <a name="status-codes-for-asynchronous-operations"></a>Códigos de estado para operações assíncronas
Uma operação assíncrona inicialmente retorna um código de estado HTTP do:

* 201 (criado)
* 202 (aceite) 

Quando a operação for concluída com êxito, ele retorna um:

* 200 (OK)
* 204 (sem conteúdo) 

Consulte a [documentação da REST API](/rest/api/) para ver as respostas para a operação que está executando.

## <a name="monitor-status-of-operation"></a>Monitorizar o estado da operação
As operações REST assíncronas devolvem valores de cabeçalho, o que utilizar para determinar o estado da operação. Existem potencialmente três valores de cabeçalho para examinar:

* `Azure-AsyncOperation` -URL para verificar o estado em curso da operação. Se a operação devolve este valor, utilizá-lo (em vez de localização) sempre para controlar o estado da operação.
* `Location` -URL para determinar quando uma operação for concluída. Utilize este valor apenas quando o Azure-AsyncOperation não é devolvido.
* `Retry-After` -O número de segundos a aguardar antes de a verificar o estado da operação assíncrona.

No entanto, nem toda operação assíncrona retorna todos esses valores. Por exemplo, terá de avaliar o valor do cabeçalho do Azure-AsyncOperation para uma operação e o valor de cabeçalho de localização para outra operação. 

Obter os valores de cabeçalho como recuperaria a qualquer valor de cabeçalho para um pedido. Por exemplo, no c#, recupera o valor de cabeçalho de uma `HttpWebResponse` com o nome do objeto `response` com o código a seguir:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Azure-AsyncOperation pedido e resposta

Para obter o estado da operação assíncrona, envie um pedido GET para o URL no valor de cabeçalho do Azure-AsyncOperation.

O corpo da resposta desta operação contém informações sobre a operação. O exemplo seguinte mostra os valores possíveis devolvidos da operação:

```json
{
    "id": "{resource path from GET operation}",
    "name": "{operation-id}", 
    "status" : "Succeeded | Failed | Canceled | {resource provider values}", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "percentComplete": {double between 0 and 100 },
    "properties": {
        /* Specific resource provider values for successful operations */
    },
    "error" : { 
        "code": "{error code}",  
        "message": "{error description}" 
    }
}
```

Apenas `status` é devolvido para todas as respostas. O objeto de erro é retornado quando o estado é com falhas ou cancelado. Todos os outros valores são opcionais; Por conseguinte, a resposta que receberá pode parecer diferente do exemplo.

## <a name="provisioningstate-values"></a>valores de provisioningState

Operações que criarem, atualizar ou eliminar (PUT, PATCH, DELETE) um recurso normalmente retornam um `provisioningState` valor. Quando uma operação for concluída, é devolvido um dos seguintes três valores: 

* Bem-sucedido
* Com Falhas
* Cancelado

Todos os outros valores indicam que a operação ainda está em execução. O fornecedor de recursos pode retornar um valor personalizado que indica o seu estado. Por exemplo, poderá receber **aceite** quando o pedido é recebido e em execução.

## <a name="example-requests-and-responses"></a>Exemplo de solicitações e respostas

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Iniciar máquina virtual (202 com o Azure-AsyncOperation)
Este exemplo mostra como determinar o estado dos **iniciar** operação para máquinas virtuais. O pedido inicial é o seguinte formato:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Ele retorna o código de estado 202. Entre os valores de cabeçalho, consulte:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Para verificar o estado da operação assíncrona, enviar outro pedido para esse URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

O corpo da resposta contém o estado da operação:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Implementar recursos (201 com o Azure-AsyncOperation)

Este exemplo mostra como determinar o estado dos **implementações** operação para implementar recursos no Azure. O pedido inicial é o seguinte formato:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

Ele retorna o código de estado 201. O corpo da resposta inclui:

```json
"provisioningState":"Accepted",
```

Entre os valores de cabeçalho, consulte:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Para verificar o estado da operação assíncrona, enviar outro pedido para esse URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

O corpo da resposta contém o estado da operação:

```json
{"status":"Running"}
```

Quando a implementação estiver concluída, a resposta contém:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Criar conta de armazenamento (com a localização e Retry-After 202)

Este exemplo mostra como determinar o estado do **criar** operação para contas de armazenamento. O pedido inicial é o seguinte formato:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

E o corpo do pedido contém propriedades para a conta de armazenamento:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Ele retorna o código de estado 202. Entre os valores de cabeçalho, verá os seguintes dois valores:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Depois de aguardar o número de segundos o estado da operação assíncrona a especificado na verificação Retry-After, ao enviar outro pedido para esse URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Se o pedido ainda está em execução, receberá um código de estado 202. Se o pedido foi concluído, seu receber um código de estado 200 e o corpo da resposta contém as propriedades da conta de armazenamento que criou.

## <a name="next-steps"></a>Passos seguintes

* Para obter documentação sobre cada operação REST, veja [documentação da REST API](/rest/api/).
* Para obter informações sobre como implementar modelos através da API de REST do Resource Manager, consulte [implementar recursos com modelos do Resource Manager e API de REST do Resource Manager](resource-group-template-deploy-rest.md).