---
title: Azure Functions manipuladores personalizados (pré-visualização)
description: Aprenda a utilizar funções Azure com qualquer versão idioma ou runtime.
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: cdbb5bbde1e5efef9bef992a62a54f1525a16df7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85052583"
---
# <a name="azure-functions-custom-handlers-preview"></a>Azure Functions manipuladores personalizados (pré-visualização)

Todas as aplicações Funções são executadas por um manipulador específico da linguagem. Embora o Azure Functions suporte muitos [manipuladores de idiomas](./supported-languages.md) por padrão, existem casos em que pode querer um controlo adicional sobre o ambiente de execução da aplicação. Os manipuladores personalizados dão-lhe este controlo adicional.

Os manipuladores personalizados são servidores web leves que recebem eventos do anfitrião Funções. Qualquer idioma que suporte primitivos HTTP pode implementar um manipulador personalizado.

Os manipuladores personalizados são os mais adequados para situações em que deseja:

- Implementar uma aplicação de função num idioma que não seja oficialmente suportado.
- Implementar uma aplicação de função numa versão idioma ou tempo de execução não suportado por padrão.
- Fornecer um maior controlo granular sobre o ambiente de execução de aplicações de função.

Com manipuladores personalizados, todos os [gatilhos e ligações de entrada e saída](./functions-triggers-bindings.md) são suportados através de [pacotes de extensão](./functions-bindings-register.md).

## <a name="overview"></a>Descrição geral

O diagrama seguinte mostra a relação entre o anfitrião Funções e um servidor web implementado como um manipulador personalizado.

![Visão geral do manipulador personalizado Azure Functions](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- Os eventos desencadeiam um pedido enviado ao anfitrião das Funções. O evento transporta uma carga http bruta (para funções acionadas por HTTP sem encadernações), ou uma carga útil que contém dados de ligação de entrada para a função.
- O anfitrião Funções, em seguida, solicita o pedido ao servidor web, emitindo uma [carga útil de pedido](#request-payload).
- O servidor web executa a função individual e devolve uma [carga útil de resposta](#response-payload) ao anfitrião Funções.
- O anfitrião funções sugere a resposta como uma carga de ligação de saída ao alvo.

Uma aplicação Azure Functions implementada como um manipulador personalizado deve configurar o *host.jse* *function.jsem* ficheiros de acordo com algumas convenções.

## <a name="application-structure"></a>Estrutura de aplicação

Para implementar um manipulador personalizado, precisa dos seguintes aspetos da sua aplicação:

- Um *host.jsno* arquivo na raiz da sua aplicação
- Uma *function.jsno* ficheiro para cada função (dentro de uma pasta que corresponde ao nome da função)
- Um comando, script ou executável, que executa um servidor web

O diagrama seguinte mostra como estes ficheiros ficam no sistema de ficheiros para uma função chamada "ordem".

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>Configuração

O pedido é configurado através do *host.jsarquivado.* Este ficheiro diz ao anfitrião funções onde enviar pedidos apontando para um servidor web capaz de processar eventos HTTP.

Um manipulador personalizado é definido configurando o *host.jsem* ficheiro com detalhes sobre como executar o servidor web através da `httpWorker` secção.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "server.exe"
        }
    }
}
```

A `httpWorker` secção aponta para um alvo tal como definido pelo `defaultExecutablePath` . O alvo de execução pode ser um comando, executável ou um ficheiro onde o servidor web é implementado.

Para aplicações `defaultExecutablePath` escritas, aponta para o tempo de execução da linguagem do script e `defaultWorkerPath` aponta para a localização do ficheiro do script. O exemplo a seguir mostra como uma aplicação JavaScript em Node.js é configurada como um manipulador personalizado.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Também pode passar argumentos usando a `arguments` matriz:

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--argument1", "--argument2" ]
        }
    }
}
```

Os argumentos são necessários para muitas configurações de depuração. Consulte a secção [Debugging](#debugging) para obter mais detalhes.

> [!NOTE]
> O *host.jsno* ficheiro deve estar ao mesmo nível na estrutura do diretório que o servidor web em execução. Alguns idiomas e porta-ferramentas podem não colocar este ficheiro na raiz da aplicação por predefinição.

#### <a name="bindings-support"></a>Suporte de encadernações

Os gatilhos padrão, juntamente com as ligações de entrada e saída, estão disponíveis referindo [pacotes de extensão](./functions-bindings-register.md) no seu *host.jsficheiro.*

### <a name="function-metadata"></a>Metadados de função

Quando usado com um manipulador personalizado, o *function.jssobre* o conteúdo não é diferente de como definiria uma função em qualquer outro contexto. O único requisito é que *function.jsem* ficheiros devem estar numa pasta nomeada para corresponder ao nome da função.

### <a name="request-payload"></a>Solicitar carga útil

A carga útil do pedido para funções HTTP pura é a carga útil de pedido http bruto. As funções HTTP pura são definidas como funções sem encadernações de entrada ou saída, que devolvem uma resposta HTTP.

Qualquer outro tipo de função que inclua entradas, encadernações de saída ou seja desencadeada através de uma fonte de evento diferente de HTTP tem uma carga útil de pedido personalizado.

O seguinte código representa uma carga útil de pedido de amostra. A carga útil inclui uma estrutura JSON com dois membros: `Data` e `Metadata` .

O `Data` membro inclui chaves que correspondem à entrada e desempoletam nomes definidos no conjunto de encadernações nofunction.js*ficheiro.*

O `Metadata` membro inclui [metadados gerados a partir da fonte do evento.](./functions-bindings-expressions-patterns.md#trigger-metadata)

Tendo em conta as encadernações definidas no seguinte *function.jsem* arquivo:

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

É devolvida uma carga útil de pedido semelhante a este exemplo:

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
| `Outputs`     | JSON      | Detém valores de resposta definidos pela `bindings` matriz que ofunction.js*em* ficheiro.<br /><br />Por exemplo, se uma função for configurada com uma saída de armazenamento blob chamada "blob", então `Outputs` contém uma chave chamada , que é definida para o valor da `blob` bolha. |
| `Logs`        | array     | As mensagens aparecem nos registos de invocação de funções.<br /><br />Ao correr em Azure, as mensagens aparecem no Application Insights. |
| `ReturnValue` | string    | Usado para fornecer uma resposta quando uma saída é configurada como `$return` no *function.jsem* arquivo. |

Veja o [exemplo para uma carga útil da amostra.](#bindings-implementation)

## <a name="examples"></a>Exemplos

Os manipuladores personalizados podem ser implementados em qualquer idioma que suporte eventos HTTP. Embora as Funções Azure [suportem totalmente o JavaScript e Node.js, ](./functions-reference-node.md)os seguintes exemplos mostram como implementar um manipulador personalizado utilizando o JavaScript em Node.js para efeitos de instrução.

> [!TIP]
> Apesar de ser um guia para aprender a implementar um manipulador personalizado em outras línguas, os exemplos baseados em Node.js mostrados aqui também podem ser úteis se quiser executar uma aplicação De Funções numa versão não suportada de Node.js.

## <a name="http-only-function"></a>Função apenas HTTP

O exemplo a seguir demonstra como configurar uma função desencadeada por HTTP sem encadernações ou saídas adicionais. O cenário implementado neste exemplo apresenta uma função nomeada `http` que aceita um ou `GET` `POST` .

O seguinte corte representa a forma como um pedido para a função é composto.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>Implementação

Numa pasta com o nome *http,* o *function.jsno* ficheiro configura a função acionada por HTTP.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
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

Na raiz da aplicação, o *host.jsem* ficheiro está configurado para executar Node.js e apontar o `server.js` ficheiro.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

O ficheiro *server.js* implementa uma função web server e HTTP.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.get("/hello", (req, res) => {
  res.json("Hello World!");
});

app.post("/hello", (req, res) => {
  res.json({ value: req.body });
});
```

Neste exemplo, o Express é utilizado para criar um servidor web para lidar com eventos HTTP e está definido para ouvir pedidos através do `FUNCTIONS_HTTPWORKER_PORT` .

A função é definida no caminho de `/hello` . `GET`os pedidos são tratados devolvendo um simples objeto JSON, e `POST` os pedidos têm acesso ao organismo de pedido via `req.body` .

A rota para a função de encomenda aqui é `/hello` e não porque o `/api/hello` anfitrião de Funções está procurando o pedido para o manipulador personalizado.

>[!NOTE]
>O `FUNCTIONS_HTTPWORKER_PORT` porto de frente para o público não é usado para chamar a função. Esta porta é utilizada pelo anfitrião Funções para chamar o manipulador personalizado.

## <a name="function-with-bindings"></a>Função com encadernações

O cenário implementado neste exemplo apresenta uma função nomeada `order` que aceita uma com uma carga útil representando uma encomenda de `POST` produto. Como uma encomenda é publicada na função, uma mensagem de armazenamento de fila é criada e uma resposta HTTP é devolvida.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
content-type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

<a id="bindings-implementation" name="bindings-implementation"></a>

### <a name="implementation"></a>Implementação

Numa pasta denominada *ordem,* o *function.jsno* ficheiro configura a função acionada por HTTP.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
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

Na raiz da aplicação, o *host.jsem* ficheiro está configurado para executar Node.js e apontar o `server.js` ficheiro.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

O ficheiro *server.js* implementa uma função web server e HTTP.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.post("/order", (req, res) => {
  const message = req.body.Data.req.Body;
  const response = {
    Outputs: {
      message: message,
      res: {
        statusCode: 200,
        body: "Order complete"
      }
    },
    Logs: ["order processed"]
  };
  res.json(response);
});
```

Neste exemplo, o Express é utilizado para criar um servidor web para lidar com eventos HTTP e está definido para ouvir pedidos através do `FUNCTIONS_HTTPWORKER_PORT` .

A função é definida no caminho de `/order` .  A rota para a função de encomenda aqui é `/order` e não porque o `/api/order` anfitrião de Funções está procurando o pedido para o manipulador personalizado.

À medida que `POST` os pedidos são enviados para esta função, os dados são expostos através de alguns pontos:

- O corpo de pedido está disponível através de`req.body`
- Os dados publicados na função estão disponíveis através de`req.body.Data.req.Body`

A resposta da função é formatada num par de teclas/valor onde o `Outputs` membro detém um valor JSON onde as teclas correspondem às saídas definidas no *function.jsno* ficheiro.

Ao definir `message` igual à mensagem que veio do pedido, e `res` à resposta HTTP esperada, esta função produz uma mensagem para o Armazenamento da Fila e devolve uma resposta HTTP.

## <a name="debugging"></a>Depurar

Para depurar a sua aplicação de manipulador personalizado Functions, precisa adicionar argumentos apropriados para o idioma e tempo de execução para permitir a depuragem.

Por exemplo, para depurar um pedido de Node.js, a `--inspect` bandeira é passada como argumento nohost.js*arquivado.*

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--inspect" ]
        }
    }
}
```

> [!NOTE]
> A configuração de depurar faz parte do seu *host.jsem* ficheiro, o que significa que pode ter de remover alguns argumentos antes de ser implementado para a produção.

Com esta configuração, pode iniciar o processo de anfitrião da Função utilizando o seguinte comando:

```bash
func host start
```

Uma vez iniciado o processo, pode anexar um depurado e bater pontos de rutura.

### <a name="visual-studio-code"></a>Visual Studio Code

O exemplo a seguir é uma configuração de amostra que demonstra como pode configurar o seu *launch.jsno* ficheiro para ligar a sua aplicação ao depurar código do estúdio visual.

Este exemplo é para Node.js, por isso poderá ter de alterar este exemplo para outras línguas ou tempos de execução.

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Attach to Node Functions",
      "type": "node",
      "request": "attach",
      "port": 9229,
      "preLaunchTask": "func: host start"
    }
  ]
}
```

## <a name="deploying"></a>Implantação

Um manipulador personalizado pode ser implantado em quase todas as opções de hospedagem de Funções Azure (ver [restrições).](#restrictions) Se o seu manipulador necessitar de dependências personalizadas (como um tempo de execução do idioma), poderá ter de utilizar um [recipiente personalizado](./functions-create-function-linux-custom-image.md).

Para implementar uma aplicação de manipulador personalizado utilizando ferramentas core de funções Azure, execute o seguinte comando.

```bash
func azure functionapp publish $functionAppName --no-build --force
```

## <a name="restrictions"></a>Restrições

- O servidor web tem de começar dentro de 60 segundos.

## <a name="samples"></a>Amostras

Consulte as [amostras de manipulador personalizado GitHub repo](https://github.com/Azure-Samples/functions-custom-handlers) para exemplos de como implementar funções em diferentes línguas.
