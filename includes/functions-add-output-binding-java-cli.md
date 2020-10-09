---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 2b2c043e70aac14c7fc6f0b58aae257624b05d13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80673264"
---
Num projeto java, as ligações são definidas como anotações vinculativas no método de função. O *function.jsem* ficheiro é então autogerado com base nestas anotações.

Navegue até à localização do seu código de função em _src/main/java,_ abra o ficheiro do projeto *Function.java* e adicione o seguinte parâmetro à definição do `run` método:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

O `msg` parâmetro é um [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) tipo, que representa uma coleção de cordas que são escritas como mensagens para uma ligação de saída quando a função termina. Neste caso, a saída é uma fila de armazenamento chamada `outqueue` . O fio de ligação da conta de armazenamento é definido pelo `connection` método. Em vez da própria cadeia de ligação, passa-se a definição de aplicação que contém a cadeia de ligação da conta de armazenamento.

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