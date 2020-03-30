---
title: Estado das operações assíncronas
description: Descreve como rastrear operações assíncronas em Azure. Mostra os valores que usa para obter o estatuto de uma operação de longa duração.
ms.topic: conceptual
ms.date: 12/09/2018
ms.custom: seodec18
ms.openlocfilehash: 1cf8898e5fd63e35447f6580e13347ba6d7fc413
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485445"
---
# <a name="track-asynchronous-azure-operations"></a>Acompanhar operações assíncronas do Azure
Algumas operações do Azure REST funcionam assincronicamente porque a operação não pode ser concluída rapidamente. Este artigo descreve como rastrear o estado das operações assíncronas através de valores devolvidos na resposta.  

## <a name="status-codes-for-asynchronous-operations"></a>Códigos de estado para operações assíncronas
Uma operação assíncrona devolve inicialmente um código de estado HTTP de:

* 201 (Criado)
* 202 (Aceite) 

Quando a operação termina com sucesso, regressa:

* 200 (OK)
* 204 (Sem Conteúdo) 

Consulte a documentação rest [API](/rest/api/) para ver as respostas para a operação que está a executar.

## <a name="monitor-status-of-operation"></a>Monitorizar o estado de funcionamento
As operações assíncronas REST devolvem os valores do cabeçalho, que utiliza para determinar o estado da operação. Existem potencialmente três valores cabeçalhos para examinar:

* `Azure-AsyncOperation`- URL para verificar o estado em curso da operação. Se a sua operação devolver este valor, utilize-o sempre (em vez de Localização) para acompanhar o estado da operação.
* `Location`- URL para determinar quando uma operação está concluída. Utilize este valor apenas quando a Operação Azure-Async não for devolvida.
* `Retry-After`- O número de segundos a guardar antes de verificar o estado da operação assíncrona.

No entanto, nem todas as operações assíncronas devolvem todos estes valores. Por exemplo, poderá ser necessário avaliar o valor do cabeçalho da Operação Azure-Asyncpara uma operação e o valor do cabeçalho de localização para outra operação. 

Recupera os valores do cabeçalho como obteria qualquer valor de cabeçalho para um pedido. Por exemplo, em C#, você recupera `HttpWebResponse` o `response` valor cabeçalho de um objeto chamado com o seguinte código:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Pedido e resposta da Operação Azure-Async

Para obter o estado da operação assíncrona, envie um pedido GET para o URL em valor de cabeçalho Azure-AsyncOperation.

O corpo da resposta desta operação contém informações sobre a operação. O exemplo que se segue mostra os valores possíveis devolvidos da operação:

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

Só `status` é devolvido para todas as respostas. O objeto de erro é devolvido quando o estado é falhado ou cancelado. Todos os outros valores são opcionais; portanto, a resposta que recebe pode parecer diferente do exemplo.

## <a name="provisioningstate-values"></a>valores do Estado de fornecimento

Operações que criam, atualizam ou eliminam (PUT, PATCH, DELETE) um recurso normalmente devolve um `provisioningState` valor. Quando uma operação estiver concluída, um dos seguintes três valores é devolvido: 

* Bem-sucedido
* Falhou
* Cancelado

Todos os outros valores indicam que a operação ainda está em curso. O fornecedor de recursos pode devolver um valor personalizado que indique o seu estado. Por exemplo, pode receber **aceite** quando o pedido for recebido e em execução.

## <a name="example-requests-and-responses"></a>Pedidos e respostas de exemplo

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Iniciar máquina virtual (202 com Operação Azure-Async)
Este exemplo mostra como determinar o estado de **funcionamento** do início das máquinas virtuais. O pedido inicial encontra-se no seguinte formato:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Devolve o código de estado 202. Entre os valores cabeçalho, vê-se:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Para verificar o estado da operação assíncrona, envie outro pedido para esse URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

O organismo de resposta contém o estado da operação:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Recursos de implantação (201 com a Azure-AsyncOperation)

Este exemplo mostra como determinar o estado da operação de **implantação** de recursos para o Azure. O pedido inicial encontra-se no seguinte formato:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

Devolve o código de estado 201. O corpo da resposta inclui:

```json
"provisioningState":"Accepted",
```

Entre os valores cabeçalho, vê-se:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Para verificar o estado da operação assíncrona, envie outro pedido para esse URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

O organismo de resposta contém o estado da operação:

```json
{"status":"Running"}
```

Quando a implantação estiver terminada, a resposta contém:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Criar conta de armazenamento (202 com Localização e Retry-After)

Este exemplo mostra como determinar o estado da **operação de criação** de contas de armazenamento. O pedido inicial encontra-se no seguinte formato:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

E o organismo de pedido contém propriedades para a conta de armazenamento:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Devolve o código de estado 202. Entre os valores cabeçalho, vê-se os dois valores seguintes:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Depois de esperar pelo número de segundos especificados no Retry-After, verifique o estado da operação assíncrona enviando outro pedido para esse URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Se o pedido ainda estiver em execução, receberá um código de estado 202. Se o pedido tiver sido concluído, o seu receberá um código de estado 200, e o corpo da resposta contém as propriedades da conta de armazenamento que foi criada.

## <a name="next-steps"></a>Passos seguintes

* Para obter documentação sobre cada operação REST, consulte a [documentação REST API](/rest/api/).
* Para obter informações sobre a implementação de modelos através da API REST Manager de Recursos, consulte [recursos de implantação com modelos](../templates/deploy-rest.md)de Gestor de Recursos e Gestor de Recursos REST API .