---
title: Azure Functions manipuladores personalizados
description: Aprenda a utilizar funções Azure com qualquer versão idioma ou runtime.
author: anthonychu
ms.author: antchu
ms.date: 12/1/2020
ms.topic: article
ms.openlocfilehash: dd112c74ea9f013a0e14bddd735060ddbf73c14e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578521"
---
# <a name="azure-functions-custom-handlers"></a>Azure Functions manipuladores personalizados

Todas as aplicações Funções são executadas por um manipulador específico da linguagem. Embora as Funções Azure suportem por defeito [muitos manipuladores de linguagem,](./supported-languages.md) existem casos em que poderá querer utilizar outras línguas ou tempos de execução.

Os manipuladores personalizados são servidores web leves que recebem eventos do anfitrião Funções. Qualquer idioma que suporte primitivos HTTP pode implementar um manipulador personalizado.

Os manipuladores personalizados são os mais adequados para situações em que deseja:

- Implementar uma aplicação de função num idioma que não é suportado atualmente, como Go ou Rust.
- Implemente uma aplicação de função num tempo de execução que não seja suportado atualmente, como o Deno.

Com manipuladores personalizados, pode utilizar [gatilhos e entradas e encadernações](./functions-triggers-bindings.md) de saída através [de pacotes de extensão](./functions-bindings-register.md).

Começa com os manipuladores personalizados da Azure Functions com [quickstarts em Go e Rust.](create-first-function-vs-code-other.md)

## <a name="overview"></a>Descrição Geral

O diagrama seguinte mostra a relação entre o anfitrião Funções e um servidor web implementado como um manipulador personalizado.

![Visão geral do manipulador personalizado Azure Functions](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

1. Cada evento desencadeia um pedido enviado ao anfitrião funções. Um evento é qualquer gatilho que seja suportado por Azure Functions.
1. O anfitrião Funções emite então uma [carga útil](#request-payload) de pedido para o servidor web. A carga útil contém dados de ligação de gatilho e entrada e outros metadados para a função.
1. O servidor web executa a função individual e devolve uma [carga útil de resposta](#response-payload) ao anfitrião Funções.
1. O anfitrião funções transmite dados da resposta às ligações de saída da função para o processamento.

Uma aplicação Azure Functions implementada como um manipulador personalizado deve configurar o *host.jsem*, *local.settings.jsem*, efunction.js *em* ficheiros de acordo com algumas convenções.

## <a name="application-structure"></a>Estrutura de aplicação

Para implementar um manipulador personalizado, precisa dos seguintes aspetos da sua aplicação:

- Um *host.jsno* arquivo na raiz da sua aplicação
- Um *local.settings.jsno* arquivo na raiz da sua aplicação
- Uma *function.jsno* ficheiro para cada função (dentro de uma pasta que corresponde ao nome da função)
- Um comando, script ou executável, que executa um servidor web

O diagrama seguinte mostra como estes ficheiros ficam no sistema de ficheiros para uma função chamada "MyQueueFunction" e um manipulador personalizado que é executado *com o* nomehandler.exe.

```bash
| /MyQueueFunction
|   function.json
|
| host.json
| local.settings.json
| handler.exe
```

### <a name="configuration"></a>Configuração

A aplicação é configurada através do *host.js* e *local.settings.jsem* ficheiros.

#### <a name="hostjson"></a>host.jsem

*host.jsdiz* ao anfitrião funções onde enviar pedidos apontando para um servidor web capaz de processar eventos HTTP.

Um manipulador personalizado é definido configurando o *host.jsem* ficheiro com detalhes sobre como executar o servidor web através da `customHandler` secção.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  }
}
```

A `customHandler` secção aponta para um alvo tal como definido pelo `defaultExecutablePath` . O alvo de execução pode ser um comando, executável ou um ficheiro onde o servidor web é implementado.

Use a `arguments` matriz para passar quaisquer argumentos para o executável. Os argumentos suportam a expansão de variáveis ambientais (configurações de `%%` aplicação) utilizando notação.

Também pode alterar o diretório de trabalho utilizado pelo executável com `workingDirectory` .

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "app/handler.exe",
      "arguments": [
        "--database-connection-string",
        "%DATABASE_CONNECTION_STRING%"
      ],
      "workingDirectory": "app"
    }
  }
}
```

##### <a name="bindings-support"></a>Suporte de encadernações

Os gatilhos padrão, juntamente com as ligações de entrada e saída, estão disponíveis referindo [pacotes de extensão](./functions-bindings-register.md) no seu *host.jsficheiro.*

#### <a name="localsettingsjson"></a>local.settings.json

*local.settings.jsdefine* as definições de aplicação utilizadas na execução da aplicação de função localmente. Como pode conter segredos, *local.settings.jsdevem* ser excluídos do controlo de origem. Em Azure, utilize as definições de aplicação.

Para manipuladores personalizados, `FUNCTIONS_WORKER_RUNTIME` desemando `Custom` *emlocal.settings.js.*

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "Custom"
  }
}
```

### <a name="function-metadata"></a>Metadados de função

Quando usado com um manipulador personalizado, o *function.jssobre* o conteúdo não é diferente de como definiria uma função em qualquer outro contexto. O único requisito é que *function.jsem* ficheiros devem estar numa pasta nomeada para corresponder ao nome da função.

Os *function.jsseguintes configuram* uma função que tem um gatilho de fila e uma ligação de saída de fila. Por estar numa pasta chamada *MyQueueFunction,* define uma função chamada *MyQueueFunction*.

**MyQueueFunction/function.jsem**

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

### <a name="request-payload"></a>Solicitar carga útil

Quando uma mensagem de fila é recebida, o anfitrião Funções envia um pedido de publicação HTTP ao manipulador personalizado com uma carga útil no corpo.

O seguinte código representa uma carga útil de pedido de amostra. A carga útil inclui uma estrutura JSON com dois membros: `Data` e `Metadata` .

O `Data` membro inclui chaves que correspondem à entrada e desempoletam nomes definidos no conjunto de encadernações nofunction.js *ficheiro.*

O `Metadata` membro inclui [metadados gerados a partir da fonte do evento.](./functions-bindings-expressions-patterns.md#trigger-metadata)

```json
{
  "Data": {
    "myQueueItem": "{ message: \"Message sent\" }"
  },
  "Metadata": {
    "DequeueCount": 1,
    "ExpirationTime": "2019-10-16T17:58:31+00:00",
    "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
    "InsertionTime": "2019-10-09T17:58:31+00:00",
    "NextVisibleTime": "2019-10-09T18:08:32+00:00",
    "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
    "sys": {
      "MethodName": "QueueTrigger",
      "UtcNow": "2019-10-09T17:58:32.2205399Z",
      "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
    }
  }
}
```

### <a name="response-payload"></a>Carga útil de resposta

Por convenção, as respostas da função são formatadas como pares chave/valor. As chaves suportadas incluem:

| <nobr>Chave de carga útil</nobr>   | Tipo de dados | Observações                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | objeto    | Detém valores de resposta definidos pela `bindings` matriz emfunction.js *em*.<br /><br />Por exemplo, se uma função for configurada com uma ligação de saída de fila chamada "myQueueOutput", então `Outputs` contém uma chave chamada , que é definida pelo manipulador personalizado para as `myQueueOutput` mensagens que são enviadas para a fila. |
| `Logs`        | matriz     | As mensagens aparecem nos registos de invocação de funções.<br /><br />Ao correr em Azure, as mensagens aparecem no Application Insights. |
| `ReturnValue` | string    | Usado para fornecer uma resposta quando uma saída é configurada como `$return` no *function.jsem* arquivo. |

Este é um exemplo de uma carga útil de resposta.

```json
{
  "Outputs": {
    "res": {
      "body": "Message enqueued"
    },
    "myQueueOutput": [
      "queue message 1",
      "queue message 2"
    ]
  },
  "Logs": [
    "Log message 1",
    "Log message 2"
  ],
  "ReturnValue": "{\"hello\":\"world\"}"
}
```

## <a name="examples"></a>Exemplos

Os manipuladores personalizados podem ser implementados em qualquer idioma que suporte a receber eventos HTTP. Os exemplos a seguir mostram como implementar um manipulador personalizado utilizando a linguagem de programação Go.

### <a name="function-with-bindings"></a>Função com encadernações

O cenário implementado neste exemplo apresenta uma função nomeada `order` que aceita uma com uma carga útil representando uma encomenda de `POST` produto. Como uma encomenda é publicada na função, uma mensagem de armazenamento de fila é criada e uma resposta HTTP é devolvida.

<a id="bindings-implementation" name="bindings-implementation"></a>

#### <a name="implementation"></a>Implementação

Numa pasta denominada *ordem,* o *function.jsno* ficheiro configura a função acionada por HTTP.

**ordem/function.jsem**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Esta função é definida como uma [função detonada HTTP](./functions-bindings-http-webhook-trigger.md) que devolve uma [resposta HTTP](./functions-bindings-http-webhook-output.md) e produz uma mensagem de armazenamento [de fila.](./functions-bindings-storage-queue-output.md)

Na raiz da aplicação, o *host.jsem* ficheiro está configurado para executar um ficheiro executável chamado `handler.exe` `handler` (em Linux ou macOS).

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[1.*, 2.0.0)"
  }
}
```

Este é o pedido HTTP enviado para o tempo de execução das Funções.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
Content-Type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

O tempo de execução das Funções enviará então o seguinte pedido HTTP ao manipulador personalizado:

```http
POST http://127.0.0.1:<FUNCTIONS_CUSTOMHANDLER_PORT>/order HTTP/1.1
Content-Type: application/json

{
  "Data": {
    "req": {
      "Url": "http://localhost:7071/api/order",
      "Method": "POST",
      "Query": "{}",
      "Headers": {
        "Content-Type": [
          "application/json"
        ]
      },
      "Params": {},
      "Body": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}"
    }
  },
  "Metadata": {
  }
}
```

> [!NOTE]
> Algumas partes da carga foram removidas por brevidade.

*handler.exe* é o programa de manipulador personalizado Go compilado que executa um servidor web e responde aos pedidos de invocação de funções do anfitrião funções.

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "os"
)

type InvokeRequest struct {
    Data     map[string]json.RawMessage
    Metadata map[string]interface{}
}

type InvokeResponse struct {
    Outputs     map[string]interface{}
    Logs        []string
    ReturnValue interface{}
}

func orderHandler(w http.ResponseWriter, r *http.Request) {
    var invokeRequest InvokeRequest

    d := json.NewDecoder(r.Body)
    d.Decode(&invokeRequest)

    var reqData map[string]interface{}
    json.Unmarshal(invokeRequest.Data["req"], &reqData)

    outputs := make(map[string]interface{})
    outputs["message"] = reqData["Body"]

    resData := make(map[string]interface{})
    resData["body"] = "Order enqueued"
    outputs["res"] = resData
    invokeResponse := InvokeResponse{outputs, nil, nil}

    responseJson, _ := json.Marshal(invokeResponse)

    w.Header().Set("Content-Type", "application/json")
    w.Write(responseJson)
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/order", orderHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

Neste exemplo, o manipulador personalizado executa um servidor web para lidar com eventos HTTP e está definido para ouvir pedidos através do `FUNCTIONS_CUSTOMHANDLER_PORT` .

Mesmo que o anfitrião de Funções tenha recebido um pedido HTTP `/api/order` original, invoca o manipulador personalizado utilizando o nome da função (o nome da pasta). Neste exemplo, a função é definida no caminho de `/order` . O anfitrião envia ao manipulador personalizado um pedido HTTP no caminho de `/order` .

À medida que `POST` os pedidos são enviados para esta função, os dados de gatilho e metadados de função estão disponíveis através do organismo de pedidos HTTP. O corpo de pedido HTTP original pode ser acedido na carga `Data.req.Body` útil.

A resposta da função é formatada em pares chave/valor onde o `Outputs` membro detém um valor JSON onde as teclas correspondem às saídas definidas no *function.jsno* ficheiro.

Este é um exemplo de carga útil que este manipulador retorna ao anfitrião Funções.

```json
{
  "Outputs": {
    "message": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}",
    "res": {
      "body": "Order enqueued"
    }
  },
  "Logs": null,
  "ReturnValue": null
}
```

Ao definir a `message` saída igual aos dados de encomenda que vieram do pedido, as saídas de função que encomendam dados para a fila configurada. O anfitrião funções também devolve a resposta HTTP configurada `res` ao chamador.

### <a name="http-only-function"></a>Função apenas HTTP

Para funções desencadeadas por HTTP sem encadernações ou saídas adicionais, poderá querer que o seu manipulador trabalhe diretamente com o pedido e resposta HTTP em vez do pedido de [pedido](#request-payload) personalizado e das cargas de [resposta](#response-payload) personalizadas. Este comportamento pode ser configurado em *host.jsna* utilização da `enableForwardingHttpRequest` definição.

> [!IMPORTANT]
> O principal objetivo da funcionalidade de manipuladores personalizados é ativar idiomas e tempos de execução que não têm atualmente suporte de primeira classe em Funções Azure. Embora possa ser possível executar aplicações web usando manipuladores personalizados, a Azure Functions não é um proxy padrão reverso. Algumas funcionalidades, como o streaming de resposta, HTTP/2 e WebSockets não estão disponíveis. Alguns componentes do pedido HTTP, tais como certos cabeçalhos e rotas, podem ser restringidos. A sua aplicação também pode experimentar [um arranque a frio](event-driven-scaling.md#cold-start)excessivo.
>
> Para resolver estas circunstâncias, considere executar as suas aplicações web no [Azure App Service](../app-service/overview.md).

O exemplo a seguir demonstra como configurar uma função desencadeada por HTTP sem encadernações ou saídas adicionais. O cenário implementado neste exemplo apresenta uma função nomeada `hello` que aceita um ou `GET` `POST` .

<a id="hello-implementation" name="hello-implementation"></a>

#### <a name="implementation"></a>Implementação

Numa pasta denominada *Hello,* o *function.jsno* ficheiro configura a função acionada por HTTP.

**Olá/function.jsem**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

A função está configurada para aceitar tanto `GET` e `POST` pedidos e o valor do resultado é fornecido através de um argumento denominado `res` .

Na raiz da aplicação, o *host.jsem* ficheiro está configurado para ser executado `handler.exe` e está definido para `enableForwardingHttpRequest` `true` .

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    },
    "enableForwardingHttpRequest": true
  }
}
```

Quando `enableForwardingHttpRequest` é , o comportamento das `true` funções apenas HTTP difere do comportamento de manipuladores personalizados padrão desta forma:

* O pedido HTTP não contém os manipuladores personalizados [que solicitam](#request-payload) a carga útil. Em vez disso, o anfitrião funções invoca o manipulador com uma cópia do pedido HTTP original.
* O anfitrião funções invoca o manipulador com o mesmo caminho que o pedido original, incluindo quaisquer parâmetros de cadeia de consulta.
* O anfitrião funções devolve uma cópia da resposta HTTP do manipulador como resposta ao pedido original.

Segue-se um pedido DEM ao anfitrião de Funções. Em seguida, o anfitrião funções envia uma cópia do pedido ao manipulador personalizado no mesmo caminho.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
Content-Type: application/json

{
  "message": "Hello World!"
}
```

O ficheiro *do manipulador de ficheiros.go* implementa um servidor web e função HTTP.

```go
package main

import (
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
)

func helloHandler(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    if r.Method == "GET" {
        w.Write([]byte("hello world"))
    } else {
        body, _ := ioutil.ReadAll(r.Body)
        w.Write(body)
    }
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/api/hello", helloHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

Neste exemplo, o manipulador personalizado cria um servidor web para lidar com eventos HTTP e está definido para ouvir pedidos através do `FUNCTIONS_CUSTOMHANDLER_PORT` .

`GET` os pedidos são tratados devolvendo uma cadeia, e `POST` os pedidos têm acesso ao corpo de pedido.

A rota para a função de encomenda aqui é `/api/hello` , o mesmo que o pedido original.

>[!NOTE]
>O `FUNCTIONS_CUSTOMHANDLER_PORT` porto de frente para o público não é usado para chamar a função. Esta porta é utilizada pelo anfitrião Funções para chamar o manipulador personalizado.

## <a name="deploying"></a>Implantação

Um manipulador personalizado pode ser implantado em todas as opções de hospedagem de Azure Functions. Se o seu manipulador necessitar de sistema operativo ou dependências de plataformas (como um tempo de execução do idioma), poderá ter de utilizar um [recipiente personalizado](./functions-create-function-linux-custom-image.md).

Ao criar uma aplicação de função em Azure para manipuladores personalizados, recomendamos que selecione .NET Core como a pilha. No futuro, será adicionada uma pilha "Personalizada" para manipuladores personalizados.

Para implementar uma aplicação de manipulador personalizado utilizando ferramentas core de funções Azure, execute o seguinte comando.

```bash
func azure functionapp publish $functionAppName
```

> [!NOTE]
> Certifique-se de que todos os ficheiros necessários para executar o seu manipulador personalizado estão na pasta e incluídos na implementação. Se o seu manipulador personalizado for um binário executável ou tiver dependências específicas da plataforma, certifique-se de que estes ficheiros correspondem à plataforma de implementação do alvo.

## <a name="restrictions"></a>Restrições

- O servidor web do manipulador personalizado tem de começar dentro de 60 segundos.

## <a name="samples"></a>Amostras

Consulte as [amostras de manipulador personalizado GitHub repo](https://github.com/Azure-Samples/functions-custom-handlers) para exemplos de como implementar funções em diferentes línguas.

## <a name="troubleshooting-and-support"></a>Resolução de problemas e apoio

### <a name="trace-logging"></a>Registo de vestígios

Se o seu processo de manipulador personalizado não arrancar ou se tiver problemas de comunicação com o anfitrião Funções, pode aumentar o nível de registo da aplicação de função `Trace` para ver mais mensagens de diagnóstico do anfitrião.

Para alterar o nível de registo predefinido da aplicação de função, configufique a `logLevel` definição na `logging` secção dehost.js *em*.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "logging": {
    "logLevel": {
      "default": "Trace"
    }
  }
}
```

As Funções acolhem mensagens de registo extra, incluindo informações relacionadas com o processo de manipulador personalizado. Utilize os registos para investigar problemas que iniciam o seu processo de manipulador personalizado ou invoque funções no seu manipulador personalizado.

Localmente, os registos são impressos na consola.

Em Azure, [a consulta Application Insights rastreia os traços](analyze-telemetry-data.md#query-telemetry-data) para visualizar as mensagens de registo. Se a sua aplicação produzir um grande volume de registos, apenas um subconjunto de mensagens de registo são enviados para o Application Insights. [Desative](configure-monitoring.md#configure-sampling) a amostragem para garantir que todas as mensagens estão registadas.

### <a name="test-custom-handler-in-isolation"></a>Teste manipulador personalizado em isolamento

As aplicações de manipulador personalizado são um processo de servidor web, por isso pode ser útil iniciá-lo por si próprio e testar invocações de função, enviando [pedidos DEFSUS](#request-payload) usando uma ferramenta como [cURL](https://curl.haxx.se/) ou [Carteiro.](https://www.postman.com/)

Também pode utilizar esta estratégia nos seus oleodutos CI/CD para realizar testes automatizados no seu manipulador personalizado.

### <a name="execution-environment"></a>Ambiente de execução

Os manipuladores personalizados funcionam no mesmo ambiente que uma aplicação típica de Azure Functions. Teste o seu manipulador para garantir que o ambiente contém todas as dependências que precisa de ser executada. Para aplicações que requerem dependências adicionais, poderá necessitar executá-las utilizando uma imagem personalizada de [contentores](functions-create-function-linux-custom-image.md) hospedada no plano Azure Functions [Premium](functions-premium-plan.md).

### <a name="get-support"></a>Obter suporte

Se precisar de ajuda numa aplicação de função com manipuladores personalizados, pode submeter um pedido através de canais de suporte regulares. No entanto, devido à grande variedade de idiomas possíveis usados para construir aplicações personalizadas de manipuladores, o suporte não é ilimitado.

O suporte está disponível se o anfitrião funções tiver problemas de arranque ou comunicação com o processo de manipulador personalizado. Para problemas específicos do funcionamento interno do seu processo de manipulador personalizado, tais como questões com a língua ou enquadramento escolhidos, a nossa Equipa de Apoio não pode prestar assistência neste contexto.

## <a name="next-steps"></a>Passos seguintes

Começa a construir uma app Azure Functions em Go ou Rust com os [manipuladores personalizados quickstart](create-first-function-vs-code-other.md).
