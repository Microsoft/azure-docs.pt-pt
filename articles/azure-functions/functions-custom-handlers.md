---
title: Manipuladores personalizados funções azure (pré-visualização)
description: Aprenda a utilizar funções Azure com qualquer idioma ou versão de tempo de execução.
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: 5abc216e182d7becd9d6f42e0f566ee96d09c2a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79479257"
---
# <a name="azure-functions-custom-handlers-preview"></a>Manipuladores personalizados funções azure (pré-visualização)

Todas as aplicações Functions são executadas por um manipulador específico de linguagem. Enquanto o Azure Functions suporta muitos manipuladores de [idiomas](./supported-languages.md) por padrão, existem casos em que pode querer um controlo adicional sobre o ambiente de execução da aplicação. Os manipuladores personalizados dão-lhe este controlo adicional.

Os manipuladores personalizados são servidores web leves que recebem eventos do anfitrião funções. Qualquer idioma que suporte os primitivos HTTP pode implementar um manipulador personalizado.

Os manipuladores personalizados são mais adequados para situações onde você quer:

- Implementar uma aplicação Funções num idioma além das línguas oficialmente suportadas
- Implementar uma aplicação Funções numa versão idiomática ou tempo de execução não suportado por padrão
- Tenha controlo granular sobre o ambiente de execução de aplicações

Com manipuladores personalizados, todos os [gatilhos e encadernações](./functions-triggers-bindings.md) de entrada e saída são suportados através de pacotes de [extensão](./functions-bindings-register.md).

## <a name="overview"></a>Descrição geral

O diagrama seguinte mostra a relação entre o anfitrião das Funções e um servidor web implementado como um manipulador personalizado.

![Visão geral do manipulador personalizado funções azure](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- Os eventos desencadeiam um pedido enviado ao anfitrião das Funções. O evento transporta uma carga bruta http (para funções acionadas em HTTP sem encadernação), ou uma carga útil que detém dados de ligação de entrada para a função.
- O anfitrião funções então proxies o pedido para o servidor web, emitindo uma [carga útil](#request-payload)de pedido .
- O servidor web executa a função individual e devolve uma [carga de resposta](#response-payload) ao anfitrião funções.
- As Funções acolhem a resposta como uma carga de ligação de saída ao alvo.

Uma aplicação Azure Functions implementada como manipulador personalizado deve configurar os *ficheiros host.json* e *function.json* de acordo com algumas convenções.

## <a name="application-structure"></a>Estrutura de aplicação

Para implementar um manipulador personalizado, necessita dos seguintes aspetos da sua aplicação:

- Um ficheiro *host.json* na raiz da sua app
- Um ficheiro *function.json* para cada função (dentro de uma pasta que corresponda ao nome da função)
- Um comando, script ou executável, que executa um servidor web

O diagrama seguinte mostra como estes ficheiros se parecem no sistema de ficheiros para uma função chamada "encomenda".

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>Configuração

A aplicação é configurada através do ficheiro *host.json.* Este ficheiro diz ao anfitrião das Funções onde enviar pedidos apontando para um servidor web capaz de processar eventos HTTP.

Um manipulador personalizado é definido configurando o ficheiro *host.json* com detalhes `httpWorker` sobre como executar o servidor web através da secção.

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

A `httpWorker` secção aponta para um `defaultExecutablePath`alvo definido pelo . O alvo de execução pode ser um comando, executável ou ficheiro onde o servidor web é implementado.

Para aplicações `defaultExecutablePath` escritas, aponta para o `defaultWorkerPath` tempo de execução do script idioma e aponta para a localização do ficheiro script. O exemplo que se segue mostra como uma aplicação JavaScript no Node.js é configurada como um manipulador personalizado.

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

Também pode passar argumentos `arguments` usando a matriz:

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

Os argumentos são necessários para muitas configurações de depuração. Consulte a secção [Debugging](#debugging) para mais detalhes.

> [!NOTE]
> O ficheiro *host.json* deve estar ao mesmo nível na estrutura do diretório que o servidor web em execução. Alguns idiomas e cadeias de ferramentas não podem colocar o ficheiro na raiz da aplicação por defeito.

#### <a name="bindings-support"></a>Apoio às encadernações

Os gatilhos padrão juntamente com as encadernações de entrada e de saída estão disponíveis através da referência de [pacotes](./functions-bindings-register.md) de extensão no ficheiro *host.json.*

### <a name="function-metadata"></a>Metadados de função

Quando utilizado com um manipulador personalizado, o conteúdo *fun.json* não é diferente de como definiruma função em qualquer outro contexto. O único requisito é que *os ficheiros function.json* devem estar numa pasta nomeada para corresponder ao nome da função.

### <a name="request-payload"></a>Solicitar carga útil

A carga útil do pedido para funções http puras é a carga bruta de pedido http. As funções HTTP puras são definidas como funções sem ligações de entrada ou saída, que devolvem uma resposta HTTP.

Qualquer outro tipo de função que inclua a entrada, as encadernações de saída ou seja acionada através de uma fonte de evento que não seja http tem uma carga útil de pedido personalizado.

O seguinte código representa uma carga útil de pedido de amostra. A carga útil inclui uma estrutura JSON com dois membros: `Data` e `Metadata`.

O `Data` membro inclui teclas que correspondem à entrada e disparam nomes definidos na matriz de encadernação no ficheiro *função.json.*

O `Metadata` membro inclui [metadados gerados a partir da fonte do evento](./functions-bindings-expressions-patterns.md#trigger-metadata).

Dadas as encadernações definidas no seguinte ficheiro *função.json:*

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

Uma carga útil de pedido semelhante a este exemplo é devolvida:

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

Por convenção, as respostas de função são formatadas como par de chaves/valor. As chaves suportadas incluem:

| <nobr>Chave de carga útil</nobr>   | Tipo de dados | Observações                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | JSON      | Detém valores de `bindings` resposta definidos pela matriz o ficheiro *função.json.*<br /><br />Por exemplo, se uma função for configurada com uma ligação de saída de armazenamento blob chamada "blob", então `Outputs` contém uma chave chamada `blob`, que está definida para o valor da bolha. |
| `Logs`        | array     | As mensagens aparecem nos registos de invocação das Funções.<br /><br />Ao correr em Azure, as mensagens aparecem no Application Insights. |
| `ReturnValue` | string    | Usado para fornecer uma resposta quando `$return` uma saída é configurada como no ficheiro *função.json.* |

Consulte o [exemplo de uma carga útil](#bindings-implementation)da amostra .

## <a name="examples"></a>Exemplos

Os manipuladores personalizados podem ser implementados em qualquer idioma que suporte eventos HTTP. Embora as Funções Azure [suportem totalmente o JavaScript e o Node.js,](./functions-reference-node.md)os seguintes exemplos mostram como implementar um manipulador personalizado utilizando javaScript em Node.js para efeitos de instrução.

> [!TIP]
> Embora seja um guia para aprender a implementar um manipulador personalizado em outras línguas, os exemplos baseados no Nó.js mostrados aqui também podem ser úteis se você quiser executar uma app Funções em uma versão não suportada de Node.js.

## <a name="http-only-function"></a>Função http-only

O exemplo que se segue demonstra como configurar uma função desencadeada pelo HTTP sem ligações ou saídas adicionais. O cenário implementado neste exemplo apresenta `http` uma função nomeada que aceita um `GET` ou `POST` .

O seguinte corte representa a forma como é composto um pedido à função.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>Implementação

Numa pasta com o nome *http,* o ficheiro *function.json* configura a função desencadeada pelo HTTP.

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

A função está configurada para aceitar tanto e `GET` `POST` pedidos e `res`o valor do resultado é fornecido através de um argumento nomeado .

Na raiz da aplicação, o ficheiro *host.json* está configurado para `server.js` executar Node.js e apontar o ficheiro.

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

O ficheiro *file server.js* implementa um servidor web e função HTTP.

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

Neste exemplo, o Express é usado para criar um servidor web para lidar `FUNCTIONS_HTTPWORKER_PORT`com eventos HTTP e está definido para ouvir pedidos através do .

A função é definida `/hello`no caminho de . `GET`os pedidos são tratados devolvendo um simples `POST` objeto JSON, e `req.body`os pedidos têm acesso ao órgão de pedido via .

O percurso para a `/hello` função `/api/hello` de encomenda aqui é e não porque o anfitrião funções está procurando o pedido para o manipulador personalizado.

>[!NOTE]
>Não `FUNCTIONS_HTTPWORKER_PORT` é o porto virado para o público usado para chamar a função. Esta porta é utilizada pelo anfitrião funções para ligar para o manipulador personalizado.

## <a name="function-with-bindings"></a>Função com encadernações

O cenário implementado neste exemplo apresenta `order` uma função nomeada que aceita um `POST` com uma carga útil que representa uma encomenda do produto. Como uma encomenda é postada na função, uma mensagem de Armazenamento de Fila é criada e uma resposta HTTP é devolvida.

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

Numa pasta denominada *ordem,* o ficheiro *função.json* configura a função desencadeada pelo HTTP.

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

Esta função é definida como uma [função ativada](./functions-bindings-http-webhook-trigger.md) em HTTP que devolve uma [resposta HTTP](./functions-bindings-http-webhook-output.md) e produz uma mensagem de armazenamento de [fila.](./functions-bindings-storage-queue-output.md)

Na raiz da aplicação, o ficheiro *host.json* está configurado para `server.js` executar Node.js e apontar o ficheiro.

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

O ficheiro *file server.js* implementa um servidor web e função HTTP.

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

Neste exemplo, o Express é usado para criar um servidor web para lidar `FUNCTIONS_HTTPWORKER_PORT`com eventos HTTP e está definido para ouvir pedidos através do .

A função é definida `/order` no caminho de .  O percurso para a `/order` função `/api/order` de encomenda aqui é e não porque o anfitrião funções está procurando o pedido para o manipulador personalizado.

À `POST` medida que os pedidos são enviados para esta função, os dados são expostos através de alguns pontos:

- O organismo de pedido está disponível via`req.body`
- Os dados publicados na função estão disponíveis via`req.body.Data.req.Body`

A resposta da função é formatada num `Outputs` par chave/valor onde o membro detém um valor JSON onde as teclas correspondem às saídas definidas no ficheiro *função.json.*

Ao `message` definir igual à mensagem que veio `res` do pedido, e à resposta http esperada, esta função envia uma mensagem para o Armazenamento de Fila e devolve uma resposta HTTP.

## <a name="debugging"></a>Depurar

Para desinserir a aplicação de personal handler functions, é necessário adicionar argumentos adequados para o idioma e tempo de funcionamento para permitir a depuração.

Por exemplo, para depurar uma aplicação `--inspect` Node.js, a bandeira é passada como um argumento no ficheiro *host.json.*

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
> A configuração de depuração faz parte do ficheiro *host.json,* o que significa que poderá ser necessário remover alguns argumentos antes de ser utilizado para produção.

Com esta configuração, pode iniciar o processo de anfitrião da Função utilizando o seguinte comando:

```bash
func host start
```

Assim que o processo for iniciado, podes anexar um debugger e acertar pontos de rutura.

### <a name="visual-studio-code"></a>Visual Studio Code

O exemplo seguinte é uma configuração de amostra que demonstra como pode configurar o seu ficheiro *launch.json* para ligar a sua aplicação ao debugger do Código do Estúdio Visual.

Este exemplo é para Node.js, por isso pode ter que alterar este exemplo para outras línguas ou tempos de execução.

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

Um manipulador personalizado pode ser implantado em quase todas as opções de hospedagem de Funções Azure (ver [restrições).](#restrictions) Se o seu manipulador necessitar de dependências personalizadas (como um tempo de execução de idiomas), poderá ter de utilizar um [recipiente personalizado](./functions-create-function-linux-custom-image.md).

## <a name="restrictions"></a>Restrições

- Os manipuladores personalizados não são suportados nos planos de consumo do Linux.
- O servidor da web tem de começar dentro de 60 segundos.

## <a name="samples"></a>Amostras

Consulte as [amostras personalizadas do manipulador GitHub repo](https://github.com/Azure-Samples/functions-custom-handlers) para exemplos de como implementar funções em várias línguas diferentes.
