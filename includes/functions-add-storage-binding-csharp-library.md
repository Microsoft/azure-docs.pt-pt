---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 4db460a5dcefb49de3ad2b594d3957cbcf7445c3
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592788"
---
Em um C# projeto de biblioteca de classes, as associações são definidas como atributos de associação no método de função. O arquivo *Function. JSON* é gerado automaticamente com base nesses atributos.

Abra o arquivo de projeto *HttpTrigger.cs* e adicione a `using` seguinte instrução:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Adicione o seguinte parâmetro à definição `Run` do método:

```cs
[Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
```

O `msg` parâmetro é um `ICollector<T>` tipo, que representa uma coleção de mensagens que são gravadas em uma associação de saída quando a função é concluída. Nesse caso, a saída é uma fila de armazenamento chamada `outqueue`. A cadeia de conexão para a conta de armazenamento é definida `StorageAccountAttribute`pelo. Esse atributo indica a configuração que contém a cadeia de conexão da conta de armazenamento e pode ser aplicada no nível de classe, método ou parâmetro. Nesse caso, você pode omitir `StorageAccountAttribute` porque já está usando a conta de armazenamento padrão.

A definição do método Run agora deve ser semelhante ao seguinte:  

```cs
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
```
