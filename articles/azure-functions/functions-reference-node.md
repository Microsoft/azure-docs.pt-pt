---
title: Referência do desenvolvedor de JavaScript para funções do Azure | Documentos da Microsoft
description: Aprenda a desenvolver as funções com JavaScript.
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
ms.openlocfilehash: 04653dcdf0fb64e8b935cda18c01198ec91c548d
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807478"
---
# <a name="azure-functions-javascript-developer-guide"></a>Guia do Programador de JavaScript de funções do Azure

Este guia contém informações sobre as complicações de escrever as funções do Azure com o JavaScript.

Uma função de JavaScript é um exportado `function` que é executado quando acionado ([acionadores são configurados na Function](functions-triggers-bindings.md)). O primeiro argumento transmitido para cada função é um `context` objeto, que é utilizado para envio e recebimento vinculação de dados, o registo e a comunicação com o tempo de execução.

Este artigo pressupõe que já leu a [referência para programadores do funções do Azure](functions-reference.md). Concluir o início rápido das funções para criar a sua primeira função, usando [Visual Studio Code](functions-create-first-function-vs-code.md) ou [no portal do](functions-create-first-azure-function.md).

Este artigo também suporta [programação de aplicações do TypeScript](#typescript).

## <a name="folder-structure"></a>estrutura de pastas

A estrutura de pasta necessária para um projeto de JavaScript é semelhante ao seguinte. Este padrão pode ser alterado. Para obter mais informações, consulte a [scriptFile](#using-scriptfile) secção abaixo.

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
 | - bin
```

Na raiz do projeto, há um partilhada [Host. JSON](functions-host-json.md) ficheiro que pode ser utilizado para configurar a aplicação de função. Cada função tem uma pasta com o seu próprio arquivo de código (. js) e o ficheiro de configuração de vinculação (Function). O nome do `function.json`do diretório principal é sempre o nome da sua função.

As extensões de vinculação necessárias [versão 2.x](functions-versions.md) runtime das funções definidas no `extensions.csproj` arquivo, com os ficheiros de biblioteca real no `bin` pasta. Ao desenvolver localmente, deve [registar as extensões de vinculação](./functions-bindings-register.md#local-development-azure-functions-core-tools). Ao desenvolver funções no portal do Azure, este registo é feito para.

## <a name="exporting-a-function"></a>Exportar uma função

As funções JavaScript tem de ser exportadas [ `module.exports` ](https://nodejs.org/api/modules.html#modules_module_exports) (ou [ `exports` ](https://nodejs.org/api/modules.html#modules_exports)). A função exportada deve ser uma função JavaScript que é executado quando acionada.

Por predefinição, o runtime das funções procura em sua função na `index.js`, onde `index.js` compartilha o mesmo diretório principal de seu correspondente `function.json`. Na situação padrão, a função exportada deve ser a exportação única do seu ficheiro ou a exportação com o nome `run` ou `index`. Para configurar a localização do ficheiro e exportar o nome da sua função, leia sobre [configurar o ponto de entrada da sua função](functions-reference-node.md#configure-function-entry-point) abaixo.

A função exportada é passada um número de argumentos em execução. O primeiro argumento necessário é sempre um `context` objeto. Se a sua função é síncrona (não retorna uma promessa), tem de passar os `context` de objeto, como chamar `context.done` é necessário para a utilização correta.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Exportar uma função de async
Ao utilizar o [ `async function` ](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) declaração ou JavaScript simples [promessas](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) versão 2.x do runtime das funções, não é necessário chamar explicitamente o [ `context.done` ](#contextdone-method) retorno de chamada para sinalizar que a função foi concluída. Sua função é concluída quando a função/promessa exportado assíncrona for concluída. Para as funções de direcionamento o tempo de execução do versão 1.x, deve chamar ainda [ `context.done` ](#contextdone-method) quando é feito o seu código em execução.

O exemplo seguinte é uma função simples que regista o que ele foi acionado e imediatamente conclui a execução.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Ao exportar uma função de async, também pode configurar um enlace de saída para tirar o `return` valor. Isto é recomendado se tiver apenas um enlace de saída.

Para atribuir uma saída com `return`, altere o `name` propriedade `$return` no `function.json`.

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

Neste caso, sua função deve ter um aspeto semelhante ao seguinte exemplo:

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
No JavaScript, [enlaces](functions-triggers-bindings.md) são configuradas e definidos em Function uma função. Funções de interagirem com as ligações de diversas formas.

### <a name="inputs"></a>Entradas
Entrada são divididas em duas categorias nas funções do Azure: um é a entrada de Acionador e o outro é a entrada adicional. Acionador e outros enlaces de entrada (enlaces de `direction === "in"`) podem ser lidos por uma função de três formas:
 - **_[Recomendável]_  Como parâmetros transmitidos para a sua função.** Elas são passadas para a função na mesma ordem em que elas estão definidas na *Function*. O `name` propriedade definida no *Function* não tem de corresponder ao nome do seu parâmetro, embora ele deve.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Membros de, a [ `context.bindings` ](#contextbindings-property) objeto.** Com o nome por cada membro de `name` propriedade definida no *Function*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Como entradas usando o JavaScript [ `arguments` ](https://msdn.microsoft.com/library/87dw3w1k.aspx) objeto.** Isso é essencialmente o mesmo que passar entradas como parâmetros, mas permite que manipule dinamicamente entradas.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Saídas
Saídas (enlaces de `direction === "out"`) pode ser escrito por uma função de diversas formas. Em todos os casos, o `name` propriedade da ligação, conforme definido na *Function* corresponde ao nome do membro de objeto escrito na sua função. 

Pode atribuir os dados para ligações de saída de uma das seguintes formas (não o combine estes métodos):

- **_[Recomendado para várias saídas]_  Retornando um objeto.** Se estiver a utilizar uma função de retorno do async/promessa, pode retornar um objeto com dados de saída atribuído. No exemplo abaixo, as ligações de saída são com o nome "httpResponse" e "queueOutput" no *Function*.

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

  Se estiver a utilizar uma função síncrona, pode retornar este objeto utilizando [ `context.done` ](#contextdone-method) (veja o exemplo).
- **_[Recomendado para saída única]_  Retornar um valor diretamente e utilizar o nome da ligação $return.** Isso só funciona para async/promessa, retornando as funções. Veja o exemplo na [exportar uma função de async](#exporting-an-async-function). 
- **Atribuir valores para `context.bindings`**  pode atribuir valores diretamente a context.bindings.

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

### <a name="bindings-data-type"></a>Tipo de dados de associações

Para definir o tipo de dados para um enlace de entrada, utilize o `dataType` propriedade na definição de ligação. Por exemplo, para ler o conteúdo de uma solicitação HTTP no formato binário, utilize o tipo `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Opções de `dataType` são: `binary`, `stream`, e `string`.

## <a name="context-object"></a>objeto de contexto
O tempo de execução usa um `context` objeto para passar dados de e para sua função e permitir-lhe comunicar com o tempo de execução. O objeto de contexto pode ser utilizado para leitura e a definição de dados de associações, escrever os registos e usando o `context.done` retorno de chamada quando a função exportada é síncrona.

O `context` objeto é sempre o primeiro parâmetro para uma função. Devem ser incluído porque tem métodos importantes, como `context.done` e `context.log`. Pode nomear o objeto que quer que gostaria de ter (por exemplo, `ctx` ou `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>propriedade de Context.Bindings

```js
context.bindings
```

Devolve um objeto nomeado que contém todos os seus dados de entrada e saídos. Por exemplo, as seguintes definições de ligação na sua Function permitem-lhe aceder o conteúdo de uma fila de `context.bindings.myInput` e atribua saídas para uma fila usando `context.bindings.myOutput`.

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

Pode optar por definir através de dados de enlace de saída a `context.done` método em vez do `context.binding` objeto (ver abaixo).

### <a name="contextbindingdata-property"></a>propriedade de context.bindingData

```js
context.bindingData
```

Devolve um objeto nomeado que contém dados de invocação de metadados e a função de Acionador (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Para obter um exemplo de metadados de Acionador, ver isso [exemplo de hubs de eventos](functions-bindings-event-hubs.md#trigger---javascript-example).

### <a name="contextdone-method"></a>método Context.Done

```js
context.done([err],[propertyBag])
```

Permite que o tempo de execução saiba que o seu código foi concluída. Quando a função utiliza a [ `async function` ](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) declaração, não é necessário utilizar `context.done()`. O `context.done` implicitamente é chamado de retorno de chamada. Funções de Async estão disponíveis em 8 de nó ou uma versão posterior, o que requer a versão 2.x do runtime das funções.

Se a sua função não é uma função de async **tem de chamar** `context.done` para informar o tempo de execução que sua função está concluída. Os tempos de execução se está em falta.

O `context.done` método permite que passe tanto um erro definidas pelo usuário de volta para o tempo de execução e um objeto JSON que contém dados de enlace de saída. Propriedades transmitidos para `context.done` substituir qualquer item definido no `context.bindings` objeto.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>método Context.log  

```js
context.log(message)
```

Pode escrever nos registos de função de transmissão em fluxo no nível de rastreio predefinido. No `context.log`, métodos de registo adicionais estão disponíveis, que permitem que escreva registos de função em outros níveis de rastreio:


| Método                 | Descrição                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | Grava em nível de erro, registo ou inferior.   |
| **warn(_message_)**    | Grava em nível de aviso de registo ou inferior. |
| **info(_message_)**    | Grava em nível de informações registo ou inferior.    |
| **verbose(_message_)** | Escreve no registo de nível verboso.           |

O exemplo a seguir registra um log no nível de rastreio de aviso:

```javascript
context.log.warn("Something has happened."); 
```

Pode [configurar o limiar de nível de rastreio para o registo de](#configure-the-trace-level-for-console-logging) no ficheiro de Host. JSON. Para obter mais informações sobre como escrever os registos, consulte [escrever saídas de rastreio](#writing-trace-output-to-the-console) abaixo.

Leia [monitorizar as funções do Azure](functions-monitoring.md) para saber mais sobre ver e consultar os registos da função.

## <a name="writing-trace-output-to-the-console"></a>Escrever os resultados de rastreio na consola 

As funções, vai utilizar o `context.log` métodos para escrever os resultados de rastreio no console. V2.x de funções, trace produz usando `console.log` são capturados ao nível da aplicação de função. Isso significa que produz de `console.log` não estão associadas a uma invocação de função específica e, por conseguinte, não são apresentados nos registos de uma função específica. Eles, no entanto, se propaguem para o Application Insights. Não pode utilizar as funções v1.x, `console.log` para escrever no console.

Quando chama `context.log()`, a mensagem é escrita para a consola no nível de rastreio predefinido, que é o _informações_ rastrear nível. Escreve o código a seguir para a consola no nível de rastreio de informações:

```javascript
context.log({hello: 'world'});  
```

Esse código é equivalente para o código acima:

```javascript
context.log.info({hello: 'world'});  
```

Esse código grava no console no nível de erro:

```javascript
context.log.error("An error has occurred.");  
```

Uma vez _erro_ se o rastreio de mais alto nível, este rastreio é escrito para a saída em todos os níveis de rastreio, desde que o registo está ativado.

Todos os `context.log` métodos suportam o mesmo formato de parâmetro que é suportado pelo node. js [util.format método](https://nodejs.org/api/util.html#util_util_format_format). Considere o código a seguir, que escreve os registos da função utilizando o nível de rastreio predefinido:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Também pode escrever o mesmo código no seguinte formato:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Configurar o nível de rastreio para o registo da consola

As funções 1.x lhe permite que definir o nível de rastreio de limiar para escrever na consola, que facilita a controla a forma como os rastreios são escritos na consola da sua função. Para definir o limiar para todos os rastreios escritos na consola, utilize o `tracing.consoleLevel` propriedade no arquivo de Host. JSON. Esta definição aplica-se a todas as funções na sua aplicação de função. O exemplo seguinte define o limiar de rastreio para ativar o registo verboso:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Os valores da **consoleLevel** correspondem aos nomes do `context.log` métodos. Para desativar todos os registos de rastreio para a consola, defina **consoleLevel** ao _desativar_. Para obter mais informações, consulte [referência de Host. JSON](functions-host-json-v1.md).

## <a name="http-triggers-and-bindings"></a>HTTP acionadores e enlaces

HTTP e acionadores de webhook e HTTP de saída ligações usam os objetos request e response para representar a mensagem de HTTP.  

### <a name="request-object"></a>Objeto de solicitação

O `context.req` (pedido) objeto tem as seguintes propriedades:

| Propriedade      | Descrição                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Um objeto que contém o corpo do pedido.               |
| _headers_     | Um objeto que contém os cabeçalhos de pedido.                   |
| _Método_      | O método HTTP do pedido.                                |
| _originalUrl_ | O URL do pedido.                                        |
| _params_      | Um objeto que contém os parâmetros de encaminhamento do pedido. |
| _query_       | Um objeto que contém os parâmetros de consulta.                  |
| _rawBody_     | O corpo da mensagem como uma cadeia de caracteres.                           |


### <a name="response-object"></a>Objeto de resposta

O `context.res` objeto (resposta) tem as seguintes propriedades:

| Propriedade  | Descrição                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Um objeto que contém o corpo da resposta.         |
| _headers_ | Um objeto que contém os cabeçalhos de resposta.             |
| _isRaw_   | Indica que a formatação é ignorada para a resposta.    |
| _status_  | O código de estado HTTP da resposta.                     |

### <a name="accessing-the-request-and-response"></a>Aceder a solicitação e resposta 

Ao trabalhar com acionadores HTTP, pode acessar os objetos de solicitação e resposta HTTP de diversas formas:

+ **Partir `req` e `res` das propriedades no `context` objeto.** Dessa forma, pode usar o padrão convencional para dados de acesso HTTP do objeto de contexto, em vez de precisar usar toda a `context.bindings.name` padrão. O exemplo seguinte mostra como aceder a `req` e `res` objetos no `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Na entrada nomeada e enlaces de saída.** Dessa forma, o acionador HTTP e os enlaces funcionam da mesma forma como qualquer outra ligação. O exemplo seguinte define o objeto de resposta utilizando uma determinada `response` enlace: 

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
+ **_[Apenas de resposta]_  Chamando `context.res.send(body?: any)`.** Uma resposta HTTP é criada com entrada `body` como o corpo da resposta. `context.done()` é chamado implicitamente.

+ **_[Apenas de resposta]_  Chamando `context.done()`.** Um tipo especial de enlace HTTP retorna a resposta que é passada para o `context.done()` método. Enlace de saída HTTP seguinte define uma `$return` parâmetro de saída:

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

A tabela seguinte mostra a versão do node. js utilizada por cada versão principal do runtime das funções:

| Versão de funções | Versão Node.js | 
|---|---|
| 1.x | 6.11.2 (bloqueados pelo tempo de execução) |
| 2.x  | _Active Directory LTS_ e até mesmo-numerados _atual_ versões de node. js (8.11.1 e 10.14.1 recomendado). Definir a versão com o WEBSITE_NODE_DEFAULT_VERSION [definição de aplicação](functions-how-to-use-azure-function-app-settings.md#settings).|

Pode ver a versão atual que está a utilizar o tempo de execução ao verificar a definição de aplicação acima ou imprimindo `process.version` de qualquer função.

## <a name="dependency-management"></a>Gestão de dependências
Para poder utilizar as bibliotecas de Comunidade no seu código JavaScript, como mostrado no exemplo abaixo, precisa garantir que todas as dependências estão instaladas na sua aplicação de função no Azure.

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
> Deve definir um `package.json` ficheiro na raiz da sua aplicação de funções. Definir o ficheiro permite que todas as funções na aplicação, partilhar os mesmos pacotes em cache, que proporciona o melhor desempenho. Se surgir um conflito de versão, pode resolvê-lo ao adicionar um `package.json` ficheiro na pasta de uma função específica.  

Ao implementar aplicações de funções de controlo de origem, qualquer `package.json` de ficheiros presentes no seu repositório, irá acionar um `npm install` na sua pasta durante a implementação. Mas, ao implementar através do Portal ou a CLI, terá de instalar manualmente os pacotes.

Existem duas formas de instalar os pacotes em sua aplicação de funções: 

### <a name="deploying-with-dependencies"></a>Implementar com dependências
1. Instalar todos os pacotes necessários localmente ao executar `npm install`.

2. Implementar o seu código e certifique-se de que o `node_modules` pasta está incluída na implementação. 


### <a name="using-kudu"></a>Utilizar o Kudu
1. Aceda a `https://<function_app_name>.scm.azurewebsites.net`.

2. Clique em **consola de depuração** > **CMD**.

3. Aceda a `D:\home\site\wwwroot`e, em seguida, arraste o ficheiro Package. JSON para o **wwwroot** pasta na metade superior da página.  
    Também pode carregar ficheiros para a sua aplicação de função de outras formas. Para obter mais informações, consulte [como atualizar os ficheiros de aplicação de função](functions-reference.md#fileupdate). 

4. Após o carregamento do ficheiro Package JSON, execute o `npm install` comando no **consola de execução remota de Kudu**.  
    Esta ação transfere os pacotes indicados no ficheiro Package. JSON e reinicia a aplicação de funções.

## <a name="environment-variables"></a>Variáveis de ambiente

Em funções, [as definições da aplicação](functions-app-settings.md), por exemplo, a ligação de serviço são expostos cadeias de caracteres, como variáveis de ambiente durante a execução. Pode acessar essas configurações usando `process.env`, conforme mostrado aqui no `GetEnvironmentVariable` função:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Ao executar localmente, as definições da aplicação são lidos a partir da [Settings](functions-run-local.md#local-settings-file) arquivo de projeto.

## <a name="configure-function-entry-point"></a>Configurar o ponto de entrada da função

O `function.json` propriedades `scriptFile` e `entryPoint` pode ser utilizado para configurar a localização e o nome da sua função exportada. Estas propriedades podem ser importantes quando o JavaScript é transpiled.

### <a name="using-scriptfile"></a>Utilizar o `scriptFile`

Por predefinição, uma função de JavaScript é executada a partir `index.js`, um ficheiro que partilha o mesmo diretório principal de seu correspondente `function.json`.

`scriptFile` pode ser usado para obter uma estrutura de pastas é semelhante ao seguinte exemplo:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - nodeFunction.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

O `function.json` para `myNodeFunction` deve incluir um `scriptFile` propriedade apontando para o arquivo com a função exportada para executar.

```json
{
  "scriptFile": "../lib/nodeFunction.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Utilizar o `entryPoint`

Na `scriptFile` (ou `index.js`), uma função tem de ser exportada usando `module.exports` para ser encontrado e executar. Por predefinição, a função que é executado quando acionada está a exportação apenas a partir desse arquivo, a exportação com o nome `run`, ou a exportação com o nome `index`.

Isso pode ser configurado usando `entryPoint` em `function.json`, como no exemplo a seguir:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

V2.x de funções, que suporta o `this` parâmetro nas funções de utilizador, o código de função, em seguida, pode ser igual ao seguinte:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    function logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

Neste exemplo, é importante observar que embora um objeto está a ser exportado, não há garantias para preservar o estado entre execuções.

## <a name="typescript"></a>TypeScript

Quando segmenta versão 2.x do runtime das funções, ambos [as funções do Azure para Visual Studio Code](functions-create-first-function-vs-code.md) e o [ferramentas de núcleo de funções do Azure](functions-run-local.md) permitem-lhe criar aplicações de função com um modelo que suportem Projetos de aplicação de função do typeScript. O modelo gera `package.json` e `tsconfig.json` arquivos de projeto que facilitam a transpile, executar e publicar as funções JavaScript a partir do código de TypeScript com essas ferramentas.

Uma generated `.funcignore` arquivo é usado para indicar quais arquivos são excluídos quando um projeto é publicado para o Azure.  

Ficheiros typeScript (. TS) estão transpiled em arquivos JavaScript (. js) no `dist` diretório de saída. Utilizam modelos do typeScript a [ `scriptFile` parâmetro](#using-scriptfile) no `function.json` para indicar a localização do arquivo. js correspondente no `dist` pasta. A localização de saída é definida pelo modelo por meio `outDir` parâmetro no `tsconfig.json` ficheiro. Se alterar esta definição ou o nome da pasta, o tempo de execução não é possível encontrar o código para execução.

> [!NOTE]
> Existe um experimental suporte para o TypeScript versão 1.x do runtime das funções. A versão experimental transpiles ficheiros para ficheiros JavaScript quando a função é invocada o TypeScript. Na versão 2.x, esse suporte experimental foi substituído pelo método baseadas na ferramenta que faz transpilation antes que o anfitrião seja inicializado e durante o processo de implantação.

A maneira que desenvolver e implementar a partir de um projeto do TypeScript localmente depende da sua ferramenta de desenvolvimento.

### <a name="visual-studio-code"></a>Visual Studio Code

O [as funções do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) extensão permite desenvolver as suas funções usando TypeScript. As ferramentas de núcleo é um requisito da extensão das funções do Azure.

Para criar uma aplicação de funções do TypeScript no Visual Studio Code, simplesmente escolha `TypeScript` ao criar uma aplicação de funções e são-lhe pedido para escolher o idioma.

Quando pressiona **F5** para executar a aplicação localmente, transpilation é feito antes que o anfitrião (func.exe) seja inicializado. 

Ao implementar a aplicação de funções para o Azure com o **implementar a aplicação de funções...**  botão, a extensão de funções do Azure primeiro gera uma compilação prontos para produção de arquivos JavaScript os TypeScript dos arquivos de origem.

### <a name="azure-functions-core-tools"></a>Ferramentas de núcleo das funções do Azure

Para criar um projeto de aplicação de função do TypeScript usando as ferramentas de núcleo, tem de especificar a opção de linguagem typescript quando criar a sua aplicação de função. Pode fazê-lo em uma das seguintes formas:

- Executar o `func init` comando, selecione `node` como a pilha de linguagem e, em seguida, selecione `typescript`.

- Execute o comando `func init --worker-runtime typescript`.

Para executar o código de aplicação de função localmente, utilizando as ferramentas de núcleo, utilize o `npm start` comando, em vez de `func host start`. O `npm start` comando é equivalente aos seguintes comandos:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

Antes de utilizar o [ `func azure functionapp publish` ] comando para implementar no Azure, tem de executar primeiro o `npm run build:production` comando. Este comando cria uma compilação prontos para produção de arquivos JavaScript, os TypeScript dos arquivos de origem que podem ser implementados usando [ `func azure functionapp publish` ].

## <a name="considerations-for-javascript-functions"></a>Considerações para funções de JavaScript

Ao trabalhar com as funções de JavaScript, lembre-se de que as considerações nas seções a seguir.

### <a name="choose-single-vcpu-app-service-plans"></a>Escolha único vCPU planos de serviço de aplicações

Quando cria uma aplicação de funções que utiliza o plano do serviço de aplicações, recomendamos que selecione um plano de vCPU de único, em vez de um plano com vários vCPUs. Hoje em dia, funções executa funções JavaScript com mais eficiência em VMs de vCPU de único e utilizar VMs maiores não produz os aprimoramentos de desempenho esperadas. Quando for necessário, manualmente pode aumentar horizontalmente ao adicionar mais instâncias VM de vCPU de único ou pode ativar o dimensionamento automático. Para obter mais informações, consulte [dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Arranque a frio

Quando começar a desenvolver funções do Azure no modelo de alojamento sem servidor, cold são uma realidade. *Arranque a frio* refere-se ao fato de que quando a aplicação function app é iniciado pela primeira vez após um período de inatividade, demora mais tempo a iniciar cópia de segurança. Para as funções de JavaScript com árvores de dependência de grandes dimensões em particular, a frio pode ser significativa. Para acelerar o processo de arranque a frio [executar as suas funções como um ficheiro de pacote](run-functions-from-deployment-package.md) sempre que possível. Vários métodos de implementação utilizam a execução do modelo de pacote, por predefinição, mas se estiver a ter grande arranques a frio e não estão em execução desta forma, essa alteração pode oferecer uma melhoria significativa.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte os seguintes recursos:

+ [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
+ [Referência para programadores das Funções do Azure](functions-reference.md)
+ [Acionadores de funções do Azure e enlaces](functions-triggers-bindings.md)

['func azure functionapp publish']: functions-run-local.md#project-file-deployment
