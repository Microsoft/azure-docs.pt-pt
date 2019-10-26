---
title: Referência do desenvolvedor de JavaScript para Azure Functions | Microsoft Docs
description: Entenda como desenvolver funções usando JavaScript.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: funções do azure, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 02/24/2019
ms.author: glenga
ms.openlocfilehash: fbecb1d02c2d262487683cb493db2d5a8f0d1c3e
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72898948"
---
# <a name="azure-functions-javascript-developer-guide"></a>Guia do desenvolvedor do Azure Functions JavaScript

Este guia contém informações sobre as complexidades de escrever Azure Functions com JavaScript.

Uma função JavaScript é um `function` exportado que é executado quando disparado (os[gatilhos são configurados em function. JSON](functions-triggers-bindings.md)). O primeiro argumento passado para cada função é um objeto `context`, que é usado para receber e enviar dados de associação, registro em log e comunicação com o tempo de execução.

Este artigo pressupõe que você já tenha lido o [Azure Functions referência do desenvolvedor](functions-reference.md). Conclua o início rápido do Functions para criar sua primeira função, usando [Visual Studio Code](functions-create-first-function-vs-code.md) ou [no portal](functions-create-first-azure-function.md).

Este artigo também dá suporte ao [desenvolvimento de aplicativos TypeScript](#typescript).

## <a name="folder-structure"></a>Estrutura de pastas

A estrutura de pastas necessária para um projeto JavaScript é semelhante ao seguinte. Esse padrão pode ser alterado. Para obter mais informações, consulte a seção [scriptfile](#using-scriptfile) abaixo.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

Na raiz do projeto, há um arquivo [host. JSON](functions-host-json.md) compartilhado que pode ser usado para configurar o aplicativo de funções. Cada função tem uma pasta com seu próprio arquivo de código (. js) e arquivo de configuração de associação (Function. JSON). O nome do diretório pai do `function.json` é sempre o nome da sua função.

As extensões de associação necessárias na [versão 2. x](functions-versions.md) do tempo de execução do Functions são definidas no arquivo `extensions.csproj`, com os arquivos de biblioteca reais na pasta `bin`. Ao desenvolver localmente, você deve [registrar extensões de associação](./functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal do Azure, esse registro é feito para você.

## <a name="exporting-a-function"></a>Exportando uma função

As funções JavaScript devem ser exportadas via [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (ou [`exports`](https://nodejs.org/api/modules.html#modules_exports)). A função exportada deve ser uma função JavaScript que é executada quando disparada.

Por padrão, o tempo de execução do Functions procura sua função em `index.js`, onde `index.js` compartilha o mesmo diretório pai que o `function.json` correspondente. No caso padrão, a função exportada deve ser a única exportação de seu arquivo ou a exportação nomeada `run` ou `index`. Para configurar o local do arquivo e o nome de exportação de sua função, leia sobre como [Configurar o ponto de entrada da função](functions-reference-node.md#configure-function-entry-point) abaixo.

A função exportada é passada a um número de argumentos na execução. O primeiro argumento necessário é sempre um objeto `context`. Se sua função for síncrona (não retorna uma promessa), você deve passar o objeto `context`, pois a chamada a `context.done` é necessária para o uso correto.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Exportando uma função assíncrona
Ao usar a Declaração [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) ou as [promessas](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) de JavaScript simples na versão 2. x do tempo de execução do functions, você não precisa chamar explicitamente o retorno de chamada [`context.done`](#contextdone-method) para sinalizar que a função foi concluída. Sua função é concluída quando a função assíncrona/promessa exportada é concluída. Para funções direcionadas para o tempo de execução da versão 1. x, você ainda deve chamar [`context.done`](#contextdone-method) quando o seu código for executado.

O exemplo a seguir é uma função simples que registra que foi disparada e executa imediatamente a execução.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Ao exportar uma função Async, você também pode configurar uma associação de saída para obter o valor `return`. Isso é recomendado se você tiver apenas uma associação de saída.

Para atribuir uma saída usando `return`, altere a propriedade `name` para `$return` em `function.json`.

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

Nesse caso, sua função deve ser semelhante ao exemplo a seguir:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>Enlaces 
No JavaScript, as [associações](functions-triggers-bindings.md) são configuradas e definidas no function. JSON de uma função. As funções interagem com associações de várias maneiras.

### <a name="inputs"></a>Entradas
As entradas são divididas em duas categorias no Azure Functions: uma é a entrada do gatilho e a outra é a entrada adicional. Gatilho e outras associações de entrada (associações de `direction === "in"`) podem ser lidos por uma função de três maneiras:
 - **_[Recomendado]_ Como parâmetros passados para sua função.** Eles são passados para a função na mesma ordem em que são definidos em *Function. JSON*. A propriedade `name` definida em *Function. JSON* não precisa corresponder ao nome do parâmetro, embora deva ser.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Como membros do objeto [`context.bindings`](#contextbindings-property) .** Cada membro é nomeado pela propriedade `name` definida em *Function. JSON*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Como entradas usando o objeto JavaScript [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) .** Isso é essencialmente o mesmo que passar entradas como parâmetros, mas permite que você manipule dinamicamente as entradas.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Saídas
As saídas (associações de `direction === "out"`) podem ser gravadas por uma função de várias maneiras. Em todos os casos, a propriedade `name` da associação, conforme definido em *Function. JSON* , corresponde ao nome do membro do objeto gravado em sua função. 

Você pode atribuir dados a associações de saída de uma das seguintes maneiras (não Combine esses métodos):

- **_[Recomendado para várias saídas]_ Retornando um objeto.** Se você estiver usando uma função de retorno de Async/Promise, poderá retornar um objeto com os dados de saída atribuídos. No exemplo a seguir, as associações de saída são nomeadas como "httpResponse" e "queueOutput" em *Function. JSON*.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  Se você estiver usando uma função síncrona, poderá retornar esse objeto usando [`context.done`](#contextdone-method) (consulte o exemplo).
- **_[Recomendado para saída única]_ Retornando um valor diretamente e usando o nome da Associação de $return.** Isso funciona apenas para funções de retorno de Async/Promise. Consulte o exemplo em [exportando uma função Async](#exporting-an-async-function). 
- **Atribuindo valores a `context.bindings`** Você pode atribuir valores diretamente a Context. Bindings.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>Tipo de dados bindings

Para definir o tipo de dados para uma associação de entrada, use a propriedade `dataType` na definição de associação. Por exemplo, para ler o conteúdo de uma solicitação HTTP em formato binário, use o tipo `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

As opções para `dataType` são: `binary`, `stream` e `string`.

## <a name="context-object"></a>objeto Context
O tempo de execução usa um objeto `context` para passar dados de e para sua função e para permitir que você se comunique com o tempo de execução. O objeto Context pode ser usado para ler e definir dados de associações, gravar logs e usar o retorno de chamada `context.done` quando a função exportada for síncrona.

O objeto `context` sempre é o primeiro parâmetro para uma função. Ele deve ser incluído porque tem métodos importantes, como `context.done` e `context.log`. Você pode nomear o objeto como quiser (por exemplo, `ctx` ou `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>Propriedade Context. Bindings

```js
context.bindings
```

Retorna um objeto nomeado que é usado para ler ou atribuir dados de associação. Dados de associação de entrada e gatilho podem ser acessados lendo propriedades em `context.bindings`. Os dados de associação de saída podem ser atribuídos adicionando dados a `context.bindings`

Por exemplo, as definições de associação a seguir em seu function. JSON permitem acessar o conteúdo de uma fila de `context.bindings.myInput` e atribuir saídas a uma fila usando `context.bindings.myOutput`.

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

Você pode optar por definir dados de associação de saída usando o método `context.done` em vez do objeto `context.binding` (veja abaixo).

### <a name="contextbindingdata-property"></a>Propriedade Context. bindingData

```js
context.bindingData
```

Retorna um objeto nomeado que contém metadados de gatilho e dados de invocação de função (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Para obter um exemplo de metadados de gatilho, consulte este [exemplo de hubs de eventos](functions-bindings-event-hubs.md#trigger---javascript-example).

### <a name="contextdone-method"></a>método Context. Done

```js
context.done([err],[propertyBag])
```

Permite que o tempo de execução saiba que seu código foi concluído. Quando sua função usa a Declaração [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) , você não precisa usar `context.done()`. O retorno de chamada `context.done` é chamado implicitamente. As funções assíncronas estão disponíveis no nó 8 ou uma versão posterior, que requer a versão 2. x do tempo de execução do functions.

Se sua função não for uma função assíncrona, **você deverá chamar** `context.done` para informar o tempo de execução de que sua função está concluída. A execução atinge o tempo limite se ela estiver ausente.

O método `context.done` permite que você transmita de volta um erro definido pelo usuário para o tempo de execução e um objeto JSON contendo dados de associação de saída. As propriedades passadas para `context.done` substituem qualquer conjunto no objeto `context.bindings`.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>método Context. log  

```js
context.log(message)
```

Permite que você grave nos logs da função de streaming no nível de rastreamento padrão. No `context.log`, são disponibilizados métodos de log adicionais que permitem escrever logs de função em outros níveis de rastreamento:


| Método                 | Descrição                                |
| ---------------------- | ------------------------------------------ |
| **erro (_mensagem_)**   | Grava em log de nível de erro ou inferior.   |
| **aviso (_mensagem_)**    | Grava em log de nível de aviso ou inferior. |
| **informações (_mensagem_)**    | Grava no log de nível de informações ou inferior.    |
| **Detalhado (_mensagem_)** | Grava no log de nível detalhado.           |

O exemplo a seguir grava um log no nível de rastreamento de aviso:

```javascript
context.log.warn("Something has happened."); 
```

Você pode [Configurar o limite de nível de rastreamento para registro](#configure-the-trace-level-for-console-logging) no arquivo host. JSON. Para obter mais informações sobre como gravar logs, consulte [gravando saídas de rastreamento](#writing-trace-output-to-the-console) abaixo.

Leia [Azure Functions de monitoramento](functions-monitoring.md) para saber mais sobre como exibir e consultar logs de função.

## <a name="writing-trace-output-to-the-console"></a>Gravando a saída do rastreamento no console 

No functions, você usa os métodos `context.log` para gravar a saída de rastreamento no console. No functions v2. x, as saídas de rastreamento usando `console.log` são capturadas no nível de Aplicativo de funções. Isso significa que as saídas de `console.log` não estão vinculadas a uma invocação de função específica e não são exibidas em logs de uma função específica. No entanto, eles são propagados para Application Insights. No functions v1. x, você não pode usar `console.log` para gravar no console.

Quando você chama `context.log()`, sua mensagem é gravada no console no nível de rastreamento padrão, que é o nível de rastreamento de _informações_ . O código a seguir grava no console no nível de rastreamento de informações:

```javascript
context.log({hello: 'world'});  
```

Esse código é equivalente ao código acima:

```javascript
context.log.info({hello: 'world'});  
```

Esse código grava no console no nível de erro:

```javascript
context.log.error("An error has occurred.");  
```

Como o _erro_ é o nível de rastreamento mais alto, esse rastreamento é gravado na saída em todos os níveis de rastreamento, desde que o log esteja habilitado.

Todos os métodos `context.log` oferecem suporte ao mesmo formato de parâmetro que é suportado pelo [método util. Format](https://nodejs.org/api/util.html#util_util_format_format)do node. js. Considere o código a seguir, que grava logs de função usando o nível de rastreamento padrão:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Você também pode escrever o mesmo código no seguinte formato:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Configurar o nível de rastreamento para o log do console

O Functions 1. x permite que você defina o nível de rastreamento de limite para gravação no console, o que facilita o controle do modo como os rastreamentos são gravados no console a partir de sua função. Para definir o limite para todos os rastreamentos gravados no console, use a propriedade `tracing.consoleLevel` no arquivo host. JSON. Essa configuração se aplica a todas as funções em seu aplicativo de funções. O exemplo a seguir define o limite de rastreamento para habilitar o log detalhado:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Os valores de **consoleLevel** correspondem aos nomes dos métodos `context.log`. Para desabilitar todo o log de rastreamento para o console, defina **consoleLevel** como _off_. Para obter mais informações, consulte [referência de host. JSON](functions-host-json-v1.md).

## <a name="http-triggers-and-bindings"></a>Gatilhos e associações HTTP

Os gatilhos HTTP e webhook e as associações de saída HTTP usam objetos Request e Response para representar as mensagens HTTP.  

### <a name="request-object"></a>Objeto de solicitação

O objeto `context.req` (solicitação) tem as seguintes propriedades:

| Propriedade      | Descrição                                                    |
| ------------- | -------------------------------------------------------------- |
| _conteúdo_        | Um objeto que contém o corpo da solicitação.               |
| _conector_     | Um objeto que contém os cabeçalhos de solicitação.                   |
| _forma_      | O método HTTP da solicitação.                                |
| _originalUrl_ | A URL da solicitação.                                        |
| _params_      | Um objeto que contém os parâmetros de roteamento da solicitação. |
| _consultá_       | Um objeto que contém os parâmetros de consulta.                  |
| _rawBody_     | O corpo da mensagem como uma cadeia de caracteres.                           |


### <a name="response-object"></a>Objeto de resposta

O objeto `context.res` (resposta) tem as seguintes propriedades:

| Propriedade  | Descrição                                               |
| --------- | --------------------------------------------------------- |
| _conteúdo_    | Um objeto que contém o corpo da resposta.         |
| _conector_ | Um objeto que contém os cabeçalhos de resposta.             |
| _isRaw_   | Indica que a formatação é ignorada para a resposta.    |
| _Estado_  | O código de status HTTP da resposta.                     |

### <a name="accessing-the-request-and-response"></a>Acessando a solicitação e a resposta 

Ao trabalhar com gatilhos HTTP, você pode acessar os objetos de solicitação e resposta HTTP de várias maneiras:

+ **Das propriedades `req` e `res` no objeto `context`.** Dessa forma, você pode usar o padrão convencional para acessar dados HTTP do objeto de contexto, em vez de ter que usar o padrão `context.bindings.name` completo. O exemplo a seguir mostra como acessar os objetos `req` e `res` no `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Das associações de entrada e saída nomeadas.** Dessa forma, o gatilho HTTP e as associações funcionam da mesma forma que qualquer outra associação. O exemplo a seguir define o objeto de resposta usando uma associação nomeada `response`: 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[Somente resposta]_ Chamando `context.res.send(body?: any)`.** Uma resposta HTTP é criada com a entrada `body` como o corpo da resposta. `context.done()` é chamado implicitamente.

+ **_[Somente resposta]_ Chamando `context.done()`.** Um tipo especial de associação HTTP retorna a resposta que é passada para o método `context.done()`. A seguinte Associação de saída HTTP define um parâmetro de saída `$return`:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version"></a>Versão do nó

A tabela a seguir mostra a versão do node. js usada por cada versão principal do tempo de execução do Functions:

| Versão das funções | Versão do node. js | 
|---|---|
| 1.x | 6.11.2 (bloqueado pelo tempo de execução) |
| 2.x  | _Active LTS_ e _Maintenance LTS_ versões do node. js (aproximadamente, 10 recomendado). Direcione a versão no Azure definindo a configuração do [aplicativo](functions-how-to-use-azure-function-app-settings.md#settings) WEBSITE_NODE_DEFAULT_VERSION como `~10`.|

Você pode ver a versão atual que o tempo de execução está usando verificando a configuração do aplicativo acima ou imprimindo `process.version` de qualquer função.

## <a name="dependency-management"></a>Gestão de dependências
Para usar as bibliotecas da Comunidade em seu código JavaScript, como é mostrado no exemplo abaixo, você precisa garantir que todas as dependências sejam instaladas em seu Aplicativo de funções no Azure.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> Você deve definir um arquivo `package.json` na raiz do seu Aplicativo de funções. A definição do arquivo permite que todas as funções no aplicativo compartilhem os mesmos pacotes armazenados em cache, o que proporciona o melhor desempenho. Se surgir um conflito de versão, você poderá resolvê-lo adicionando um arquivo `package.json` na pasta de uma função específica.  

Ao implantar aplicativos de funções do controle do código-fonte, qualquer arquivo `package.json` presente em seu repositório, disparará um `npm install` em sua pasta durante a implantação. Mas, ao implantar por meio do portal ou da CLI, você precisará instalar os pacotes manualmente.

Há duas maneiras de instalar pacotes no seu Aplicativo de funções: 

### <a name="deploying-with-dependencies"></a>Implantando com dependências
1. Instale todos os pacotes necessários localmente executando `npm install`.

2. Implante seu código e verifique se a pasta `node_modules` está incluída na implantação. 


### <a name="using-kudu"></a>Usando kudu
1. Aceda a `https://<function_app_name>.scm.azurewebsites.net`.

2. Clique em **console de depuração** > **cmd**.

3. Vá para `D:\home\site\wwwroot` e arraste o arquivo Package. JSON para a pasta **wwwroot** na metade superior da página.  
    Você também pode carregar arquivos para seu aplicativo de funções de outras maneiras. Para obter mais informações, consulte [como atualizar arquivos do aplicativo de funções](functions-reference.md#fileupdate). 

4. Depois que o arquivo Package. JSON for carregado, execute o comando `npm install` no **console de execução remota do kudu**.  
    Essa ação baixa os pacotes indicados no arquivo Package. JSON e reinicia o aplicativo de funções.

## <a name="environment-variables"></a>Variáveis de ambiente

Em funções, [as configurações de aplicativo](functions-app-settings.md), como cadeias de conexão de serviço, são expostas como variáveis de ambiente durante a execução. Você pode acessar essas configurações usando `process.env`, conforme mostrado aqui na segunda e terceira chamadas para `context.log()` em que registramos as variáveis de ambiente `AzureWebJobsStorage` e `WEBSITE_SITE_NAME`:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Ao executar localmente, as configurações do aplicativo são lidas no arquivo de projeto [local. Settings. JSON](functions-run-local.md#local-settings-file) .

## <a name="configure-function-entry-point"></a>Configurar ponto de entrada de função

As propriedades `function.json` `scriptFile` e `entryPoint` podem ser usadas para configurar o local e o nome da sua função exportada. Essas propriedades podem ser importantes quando o JavaScript é transcompilado.

### <a name="using-scriptfile"></a>Usando `scriptFile`

Por padrão, uma função JavaScript é executada a partir de `index.js`, um arquivo que compartilha o mesmo diretório pai que o respectivo `function.json` correspondente.

`scriptFile` pode ser usado para obter uma estrutura de pastas parecida com o exemplo a seguir:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

O `function.json` para `myNodeFunction` deve incluir uma propriedade `scriptFile` apontando para o arquivo com a função exportada a ser executada.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Usando `entryPoint`

Em `scriptFile` (ou `index.js`), uma função deve ser exportada usando `module.exports` para ser encontrada e executada. Por padrão, a função que é executada quando disparado é a única exportação desse arquivo, a exportação nomeada `run` ou a exportação nomeada `index`.

Isso pode ser configurado usando `entryPoint` em `function.json`, como no exemplo a seguir:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

No functions v2. x, que dá suporte ao parâmetro `this` em funções de usuário, o código de função poderia ser como no exemplo a seguir:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

Neste exemplo, é importante observar que, embora um objeto esteja sendo exportado, não há nenhuma garantia para preservar o estado entre as execuções.

## <a name="local-debugging"></a>Depuração local

Quando iniciado com o parâmetro `--inspect`, um processo node. js escuta um cliente de depuração na porta especificada. No Azure Functions 2. x, você pode especificar argumentos para passar para o processo node. js que executa seu código adicionando a variável de ambiente ou a configuração de aplicativo `languageWorkers:node:arguments = <args>`. 

Para depurar localmente, adicione `"languageWorkers:node:arguments": "--inspect=5858"` em `Values` no arquivo [local. Settings. JSON](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) e anexe um depurador à porta 5858.

Ao depurar usando VS Code, o parâmetro `--inspect` é adicionado automaticamente usando o valor `port` no arquivo launch. JSON do projeto.

Na versão 1. x, a configuração `languageWorkers:node:arguments` não funcionará. A porta de depuração pode ser selecionada com o parâmetro [`--nodeDebugPort`](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) no Azure Functions Core Tools.

## <a name="typescript"></a>TypeScript

Quando você visa a versão 2. x do tempo de execução do functions, ambos [Azure Functions para Visual Studio Code](functions-create-first-function-vs-code.md) e o [Azure Functions Core Tools](functions-run-local.md) permitem criar aplicativos de funções usando um modelo que ofereça suporte a projetos de aplicativo de função TypeScript. O modelo gera arquivos de projeto `package.json` e `tsconfig.json` que facilitam a transcompile, a execução e a publicação de funções JavaScript a partir do código TypeScript com essas ferramentas.

Um arquivo `.funcignore` gerado é usado para indicar quais arquivos são excluídos quando um projeto é publicado no Azure.  

Os arquivos TypeScript (. TS) são transcompilados em arquivos JavaScript (. js) no diretório de saída `dist`. Os modelos do TypeScript usam o [parâmetro `scriptFile`](#using-scriptfile) no `function.json` para indicar o local do arquivo. js correspondente na pasta `dist`. O local de saída é definido pelo modelo usando o parâmetro `outDir` no arquivo `tsconfig.json`. Se você alterar essa configuração ou o nome da pasta, o tempo de execução não será capaz de localizar o código a ser executado.

> [!NOTE]
> O suporte experimental para TypeScript existe na versão 1. x do tempo de execução do functions. A versão experimental compila os arquivos TypeScript em arquivos JavaScript quando a função é invocada. Na versão 2. x, esse suporte experimental foi substituído pelo método controlado por ferramentas que faz transpilação antes do host ser inicializado e durante o processo de implantação.

A maneira como você desenvolve e implanta localmente a partir de um projeto TypeScript depende de sua ferramenta de desenvolvimento.

### <a name="visual-studio-code"></a>Visual Studio Code

O [Azure Functions para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) extensão permite desenvolver suas funções usando o TypeScript. As ferramentas principais são um requisito da extensão de Azure Functions.

Para criar um aplicativo de função TypeScript no Visual Studio Code, escolha `TypeScript` como seu idioma ao criar um aplicativo de funções.

Quando você pressiona **F5** para executar o aplicativo localmente, o transpilação é feito antes que o host (Func. exe) seja inicializado. 

Quando você implanta seu aplicativo de funções no Azure usando o botão **implantar no aplicativo de funções...** , a extensão Azure Functions primeiro gera uma compilação pronta para produção de arquivos JavaScript dos arquivos de origem do TypeScript.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Há várias maneiras pelas quais um projeto TypeScript difere de um projeto JavaScript ao usar as ferramentas principais.

#### <a name="create-project"></a>Criar o projeto

Para criar um projeto de aplicativo de função TypeScript usando ferramentas básicas, você deve especificar a opção de linguagem TypeScript ao criar seu aplicativo de funções. Você pode fazer isso de uma das seguintes maneiras:

- Execute o comando `func init`, selecione `node` como sua pilha de idiomas e, em seguida, selecione `typescript`.

- Execute o comando `func init --worker-runtime typescript`.

#### <a name="run-local"></a>Executar local

Para executar o código do aplicativo de funções localmente usando as ferramentas básicas, use os seguintes comandos em vez de `func host start`: 

```command
npm install
npm start
```

O comando `npm start` é equivalente aos seguintes comandos:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Publicar no Azure

Antes de usar o comando [`func azure functionapp publish`] para implantar no Azure, você cria uma compilação de arquivos JavaScript pronta para produção a partir dos arquivos de origem do TypeScript. 

Os comandos a seguir preparam e publicam seu projeto TypeScript usando ferramentas básicas: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

Neste comando, substitua `<APP_NAME>` pelo nome do seu aplicativo de funções.

## <a name="considerations-for-javascript-functions"></a>Considerações para funções de JavaScript

Ao trabalhar com funções JavaScript, esteja ciente das considerações nas seções a seguir.

### <a name="choose-single-vcpu-app-service-plans"></a>Escolher planos do serviço de aplicativo de vCPU único

Quando você cria um aplicativo de funções que usa o plano do serviço de aplicativo, recomendamos que você selecione um plano de vCPU único em vez de um plano com vários vCPUs. Hoje, as funções executam funções JavaScript com mais eficiência em VMs de vCPU único e o uso de VMs maiores não produz as melhorias de desempenho esperadas. Quando necessário, você pode escalar horizontalmente manualmente Adicionando mais instâncias de VM de vCPU único ou pode habilitar o dimensionamento automático. Para obter mais informações, consulte [dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Início frio

Ao desenvolver Azure Functions no modelo de hospedagem sem servidor, inícios frios são uma realidade. *Início frio* refere-se ao fato de que quando seu aplicativo de funções é iniciado pela primeira vez após um período de inatividade, demora mais para iniciar. Para funções de JavaScript com grandes árvores de dependência em particular, a inicialização a frio pode ser significativa. Para acelerar o processo de inicialização a frio, [Execute suas funções como um arquivo de pacote](run-functions-from-deployment-package.md) quando possível. Muitos métodos de implantação usam a execução do modelo de pacote por padrão, mas se você estiver experimentando uma grande inicialização a frio e não estiver sendo executado dessa forma, essa alteração poderá oferecer uma melhoria significativa.

### <a name="connection-limits"></a>Limites de conexão

Quando você usa um cliente específico do serviço em um aplicativo Azure Functions, não crie um novo cliente com cada invocação de função. Em vez disso, crie um único cliente estático no escopo global. Para obter mais informações, consulte [Managing Connections in Azure Functions](manage-connections.md).

### <a name="use-async-and-await"></a>Usar `async` e `await`

Ao escrever Azure Functions em JavaScript, você deve escrever código usando as palavras-chave `async` e `await`. Escrever código usando `async` e `await` em vez de retornos de chamada ou `.then` e `.catch` com promessas ajuda a evitar dois problemas comuns:
 - Lançar exceções não capturadas que [falham no processo node. js](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), potencialmente afetando a execução de outras funções.
 - Comportamento inesperado, como logs ausentes de Context. log, causados por chamadas assíncronas que não estão aguardando corretamente.

No exemplo a seguir, o método assíncrono `fs.readFile` é invocado com uma função de retorno de chamada de erro-primeiro como seu segundo parâmetro. Esse código causa os dois problemas mencionados acima. Uma exceção que não é detectada explicitamente no escopo correto falha em todo o processo (problema #1). Chamar `context.done()` fora do escopo da função de retorno de chamada significa que a invocação de função pode terminar antes que o arquivo seja lido (problema #2). Neste exemplo, chamar `context.done()` muito cedo resulta em entradas de log ausentes começando com `Data from file:`.

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

Usar as palavras-chave `async` e `await` ajuda a evitar esses dois erros. Você deve usar a função do utilitário node. js [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) para ativar as funções de estilo de retorno de chamada de erro primeiro em funções awaitable.

No exemplo a seguir, todas as exceções não tratadas lançadas durante a execução da função falham apenas na invocação individual que gerou uma exceção. A palavra-chave `await` significa que as etapas a seguir `readFileAsync` são executadas somente após a conclusão de `readFile`. Com `async` e `await`, você também não precisa chamar o retorno de chamada do `context.done()`.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte os seguintes recursos:

+ [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
+ [Referência para programadores das Funções do Azure](functions-reference.md)
+ [Azure Functions gatilhos e associações](functions-triggers-bindings.md)

[' Func functionapp publish ' do Azure]: functions-run-local.md#project-file-deployment
