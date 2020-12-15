---
title: Gatilho RabbitMQ para Funções Azure
description: Aprenda a executar uma Função Azure quando uma mensagem RabbitMQ é criada.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/13/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: e7095c08c385457bddf6d70d345c4f47073b4adb
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505772"
---
# <a name="rabbitmq-trigger-for-azure-functions-overview"></a>Gatilho RabbitMQ para visão geral das funções azure

> [!NOTE]
> As ligações RabbitMQ só são totalmente suportadas nos planos **do Windows Premium.** O consumo e o Linux não são atualmente apoiados.

Utilize o gatilho RabbitMQ para responder a mensagens de uma fila RabbitMQ.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](functions-bindings-rabbitmq.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que lê e regista a mensagem RabbitMQ como um [Evento RabbitMQ](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html):

```cs
[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnection")] BasicDeliverEventArgs args,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(args.Body)}");
}
```

O exemplo a seguir mostra como ler a mensagem como um POCO.

```cs
public class TestClass
{
    public string x { get; set; }
}

[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnection")] TestClass pocObj,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(pocObj)}");
}
```

Tal como acontece com os objetos Json, ocorrerá um erro se a mensagem não estiver devidamente formatada como um objeto C#. Se for, está então ligado ao pocObj variável, que pode ser usado para o que for necessário.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

O exemplo a seguir mostra uma ligação do gatilho RabbitMQ numa *function.jsno* ficheiro e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função lê e regista a mensagem RabbitMQ.

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnection"
        }
    ]
}
```

Aqui está o código do guião C:

```csx
using System;

public static void Run(string myQueueItem, ILogger log)
{
    log.LogInformation($"C# Script RabbitMQ trigger function processed: {myQueueItem}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma ligação do gatilho RabbitMQ numa *function.jsno* ficheiro e numa [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função lê e regista uma mensagem RabbitMQ.

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnection"
        }
    ]
}
```

Aqui está o código de script JavaScript:

```javascript
module.exports = async function (context, myQueueItem) {
    context.log('JavaScript RabbitMQ trigger function processed work item', myQueueItem);
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra como ler uma mensagem de fila RabbitMQ através de um gatilho.

Uma ligação RabbitMQ é definida em *function.jsno* local onde *o tipo* está definido para `RabbitMQTrigger` .

```json
{
    "scriptFile": "__init__.py",
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "",
            "connectionStringSetting": ""
        }
    ]
}
```

O código *_\_ no seu_ \_ .py* declara um parâmetro como `func.RabbitMQMessage` , que lhe permite ler a mensagem na sua função.

```python
import logging
import azure.functions as func

def main(myQueueItem) -> None:
    logging.info('Python RabbitMQ trigger function processed a queue item: %s', myQueueItem)
```

# <a name="java"></a>[Java](#tab/java)

A seguinte função Java utiliza a `@RabbitMQTrigger` anotação dos [tipos Java RabbitMQ](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq) para descrever a configuração para um gatilho de fila RabbitMQ. A função agarra a mensagem colocada na fila e adiciona-a aos registos.

```java
@FunctionName("RabbitMQTriggerExample")
public void run(
    @RabbitMQTrigger(connectionStringSetting = "rabbitMQConnection", queueName = "queue") String input,
    final ExecutionContext context)
{
    context.getLogger().info("Java HTTP trigger processed a request." + input);
}
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o atributo [RabbitMQTrigger.](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/Trigger/RabbitMQTriggerAttribute.cs)

Aqui está um `RabbitMQTrigger` atributo numa assinatura de método:

```csharp
[FunctionName("RabbitMQTest")]
public static void RabbitMQTest([RabbitMQTrigger("queue")] string message, ILogger log)
{
    ...
}
```

Para um exemplo completo, consulte o exemplo C#.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

A `RabbitMQTrigger` anotação permite-lhe criar uma função que funciona quando uma mensagem RabbitMQ é criada. As opções de configuração disponíveis incluem nome de fila e nome de cadeia de ligação. Para mais detalhes sobre parâmetros, visite as [anotações de Java RabbitMQTrigger](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQTrigger.java).

Consulte o [exemplo](#example) do gatilho para obter mais detalhes.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `RabbitMQTrigger` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/a | Deve ser definido como "RabbitMQTrigger".|
|**direção** | n/a | Deve ser definido para "dentro".|
|**nome** | n/a | O nome da variável que representa a fila no código de função. |
|**nome de fila**|**Nome da fila**| Nome da fila para receber mensagens de. |
|**nome hospedeiro**|**Nome anfitrião**|(opcional se utilizar o ConnectStringSetting) <br>Nome anfitrião da fila (Ex: 10.26.45.210)|
|**nomeamento de utilizadorNameSetting**|**Nomeagem de utilizador**|(opcional se utilizar o ConnectionStringSetting) <br>Nome para aceder à fila |
|**passwordSetting**|**Passwordsetting**|(opcional se utilizar o ConnectionStringSetting) <br>Senha para aceder à fila|
|**conexãoStringSetting**|**ConexãoStringSetting**|O nome da definição da aplicação que contém a cadeia de ligação da fila da mensagem RabbitMQ. Por favor, note que se especificar diretamente o fio de ligação e não através de uma definição de aplicação no local.settings.jsligado, o gatilho não funcionará. (Ex: Em *function.jssobre:* connectionStringSetting: "rabbitMQConnection" <br> Em *local.settings.jsem*: "rabbitMQConnection" : "< ActualConnectionstring >")|
|**porto**|**Porta**|Recebe ou define o Porto usado. Incumprimentos para 0.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C#](#tab/csharp)

Estão disponíveis os seguintes tipos de parâmetros para a mensagem:

* [RabbitMQ Event](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html) - o formato padrão para mensagens RabbitMQ.
  * `byte[]`- Através da propriedade 'Body' do Evento RabbitMQ.
* `string` - A mensagem é texto.
* `An object serializable as JSON` - A mensagem é entregue como uma cadeia JSON válida.
* `POCO` - A mensagem é formatada como um objeto C#. Para um exemplo completo, consulte o [exemplo](#example)C# .

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Estão disponíveis os seguintes tipos de parâmetros para a mensagem:

* [RabbitMQ Event](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html) - o formato padrão para mensagens RabbitMQ.
  * `byte[]`- Através da propriedade 'Body' do Evento RabbitMQ.
* `string` - A mensagem é texto.
* `An object serializable as JSON` - A mensagem é entregue como uma cadeia JSON válida.
* `POCO` - A mensagem é formatada como um objeto C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A mensagem RabbitMQ é transmitida para a função como um objeto de corda ou JSON.

# <a name="python"></a>[Python](#tab/python)

A mensagem RabbitMQ é transmitida para a função como um objeto de corda ou JSON.

# <a name="java"></a>[Java](#tab/java)

Consulte [os atributos e anotações de](#attributes-and-annotations)Java.

---

## <a name="dead-letter-queues"></a>Filas de cartas mortas
As filas e trocas de letras mortas não podem ser controladas ou configuradas a partir do gatilho RabbitMQ.  Para utilizar filas de letras mortas, pré-configurar a fila usada pelo gatilho em RabbitMQ. Consulte a [documentação RabbitMQ.](https://www.rabbitmq.com/dlx.html)

## <a name="hostjson-settings"></a>host.jsnas definições

Esta secção descreve as definições de configuração global disponíveis para esta ligação nas versões 2.x ou superiores. O exemplo *host.jsno* ficheiro abaixo contém apenas as definições para esta ligação. Para obter mais informações sobre as configurações globais, consulte [host.jsna referência para a versão Azure Functions](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            "prefetchCount": 100,
            "queueName": "queue",
            "connectionString": "amqp://user:password@url:port",
            "port": 10
        }
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------|
|prefetchCount|30|Recebe ou define o número de mensagens que o recetor de mensagens pode simultaneamente solicitar e está em cache.|
|nome de fila|n/a| Nome da fila para receber mensagens de. |
|conexãoStragem|n/a|O nome da definição da aplicação que contém a cadeia de ligação da fila da mensagem RabbitMQ. Por favor, note que se especificar diretamente o fio de ligação e não através de uma definição de aplicação no local.settings.jsligado, o gatilho não funcionará.|
|porta|0|O número máximo de sessões que podem ser manuseadas simultaneamente por instância em escala.|

## <a name="local-testing"></a>Testes locais

> [!NOTE]
> A ligaçãoStragem tem precedência sobre "hostName", "userName" e "password". Se estiverem todos prontos, a ligaçãoStragem irá anular os outros dois.

Se estiver a testar localmente sem uma cadeia de ligação, deverá definir a definição "hostName" e "username" e "password" se aplicável na secção "RabbitMQ" de *host.jsem*:

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            ...
            "hostName": "localhost",
            "username": "<your username>",
            "password": "<your password>"
        }
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------|
|nome hospedeiro|n/a|(opcional se utilizar o ConnectStringSetting) <br>Nome anfitrião da fila (Ex: 10.26.45.210)|
|userName|n/a|(opcional se utilizar o ConnectionStringSetting) <br>Nome para aceder à fila |
|palavra-passe|n/a|(opcional se utilizar o ConnectionStringSetting) <br>Senha para aceder à fila|

## <a name="monitoring-rabbitmq-endpoint"></a>Monitorização do ponto final RabbitMQ
Para monitorizar as suas filas e trocas por um determinado ponto final RabbitMQ:

* Ativar o [plugin de gestão RabbitMQ](https://www.rabbitmq.com/management.html)
* Navegue em http://{node-hostname}:15672 e faça login com o seu nome de utilizador e senha.

## <a name="next-steps"></a>Passos seguintes

- [Enviar mensagens RabbitMQ de Funções Azure (ligação de saída)](./functions-bindings-rabbitmq-output.md)
