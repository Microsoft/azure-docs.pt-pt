---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 2b2c043e70aac14c7fc6f0b58aae257624b05d13
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673264"
---
Num projeto java, as encadernações são definidas como anotações vinculativas no método da função. O ficheiro *função.json* é então autogerado com base nestas anotações.

Navegue na localização do seu código de funções sob _o sRC/main/java,_ abra o `run` ficheiro do projeto *Function.java* e adicione o seguinte parâmetro à definição de método:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

O `msg` parâmetro é [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) um tipo, que representa uma coleção de cordas que são escritas como mensagens para uma ligação de saída quando a função completa. Neste caso, a saída é `outqueue`uma fila de armazenamento chamada . A cadeia de ligação para `connection` a conta de armazenamento é definida pelo método. Em vez da cadeia de ligação em si, passa a definição de aplicação que contém a cadeia de ligação da conta de armazenamento.

A `run` definição do método deve agora parecer o seguinte exemplo:  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```