---
title: Referência do desenvolvedor JavaScript para Funções Azure
description: Entenda como desenvolver funções usando o JavaScript.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: conceptual
ms.date: 07/17/2020
ms.custom: devx-track-js
ms.openlocfilehash: 758e11a9c043fbd1238d1e3533a2d83804ec0b73
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043111"
---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions JavaScript guia de desenvolvedores

Este guia contém informações detalhadas para ajudá-lo a desenvolver funções Azure utilizando o JavaScript.

Como um Express.js, Node.js ou javaScript developer, se você é novo em Azure Functions, por favor considere primeira leitura um dos seguintes artigos:

| Introdução | Conceitos| Aprendizagem guiada |
| -- | -- | -- | 
| <ul><li>[Node.js função usando Código de Estúdio Visual](./functions-create-first-function-vs-code.md?pivots=programming-language-javascript)</li><li>[Node.js função com o terminal/comando](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-javascript)</li></ul> | <ul><li>[Guia para programadores](functions-reference.md)</li><li>[Opções de alojamento](functions-scale.md)</li><li>[Funções TypeScript](#typescript)</li><li>[&nbsp;Considerações de desempenho](functions-best-practices.md)</li></ul> | <ul><li>[Criar aplicações sem servidor](/learn/paths/create-serverless-applications/)</li><li>[Refactor Node.js e APIs express a APIs sem servidor](/learn/modules/shift-nodejs-express-apis-serverless/)</li></ul> |

## <a name="javascript-function-basics"></a>Básicos de função JavaScript

Uma função JavaScript (Node.js) é uma função exportada `function` que executa quando aciona[(os gatilhos são configurados em function.jsligados).](functions-triggers-bindings.md) O primeiro argumento passado para cada função é um `context` objeto, que é usado para receber e enviar dados vinculativos, registar e comunicar com o tempo de execução.

## <a name="folder-structure"></a>Estrutura de pasta

A estrutura de pasta necessária para um projeto JavaScript parece ser a seguinte. Este padrão pode ser alterado. Para mais informações, consulte a secção [scriptFile](#using-scriptfile) abaixo.

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

Na origem do projeto, há umahost.jspartilhada [ no](functions-host-json.md) ficheiro que pode ser usada para configurar a aplicação de função. Cada função tem uma pasta com o seu próprio ficheiro de código (.js) e ficheiro de configuração de ligação (function.jsligado). O nome do `function.json` diretório dos pais é sempre o nome da sua função.

As extensões de ligação exigidas na [versão 2.x](functions-versions.md) do tempo de execução das Funções são definidas no `extensions.csproj` ficheiro, com os ficheiros reais da biblioteca na `bin` pasta. Ao desenvolver-se localmente, deve [registar extensões vinculativas](./functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal Azure, este registo é feito para si.

## <a name="exporting-a-function"></a>Exportação de uma função

As funções JavaScript devem ser exportadas através [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (ou [`exports`](https://nodejs.org/api/modules.html#modules_exports) . A sua função exportada deve ser uma função JavaScript que executa quando ativada.

Por predefinição, o tempo de execução de Funções procura a sua função `index.js` em, onde `index.js` partilha o mesmo directório-mãe que o correspondente `function.json` . No caso de incumprimento, a sua função exportada deve ser a única exportação do seu ficheiro ou da exportação nomeada `run` ou `index` . Para configurar a localização do ficheiro e o nome de exportação da sua função, leia sobre [a configuração do ponto de entrada da sua função](functions-reference-node.md#configure-function-entry-point) abaixo.

A sua função exportada é aprovada por uma série de argumentos sobre a execução. O primeiro argumento que é preciso é sempre um `context` objeto. Se a sua função for sincronizada (não devolve uma Promessa), tem de passar o objeto, uma vez que `context` é necessária uma chamada para uma `context.done` utilização correta.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Exportação de uma função assínca
Ao utilizar a declaração ou as [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) [promessas](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) javaScript simples na versão 2.x do tempo de execução das Funções, não precisa de ligar explicitamente para o [`context.done`](#contextdone-method) chamador para sinalizar que a sua função está concluída. A sua função termina quando a função assínc exportada/Promessa estiver concluída. Para funções que direcionem a versão 1.x do tempo de execução, deve ainda ligar [`context.done`](#contextdone-method) quando o seu código estiver feito de execução.

O exemplo a seguir é uma função simples que regista que foi desencadeada e completa imediatamente a execução.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Ao exportar uma função assínca, também pode configurar uma ligação de saída para assumir o `return` valor. Isto é recomendado se tiver apenas uma ligação de saída.

Para atribuir uma saída `return` utilizando, altere a `name` propriedade para `$return` `function.json` .

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

Neste caso, a sua função deve parecer o seguinte exemplo:

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
No JavaScript, [as ligações](functions-triggers-bindings.md) são configuradas e definidas no function.jsde uma função. As funções interagem com encadernações de várias maneiras.

### <a name="inputs"></a>Entradas
A entrada é dividida em duas categorias em Funções Azure: uma é a entrada do gatilho e a outra é a entrada adicional. O gatilho e outras ligações de entrada (encadernações `direction === "in"` de) podem ser lidos por uma função de três maneiras:
 - **_[Recomendado]_ Como parâmetros passados para a sua função.** São passados para a função na mesma ordem em que são definidos em *function.jsem* . A `name` propriedade definida emfunction.js *não* precisa de corresponder ao nome do seu parâmetro, embora deva.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Como membros do [`context.bindings`](#contextbindings-property) objeto.** Cada membro é nomeado pela `name` propriedade definida emfunction.js *em* .
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Como entradas utilizando o [`arguments`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments) objeto JavaScript.** Isto é essencialmente o mesmo que passar entradas como parâmetros, mas permite-lhe lidar dinamicamente com as entradas.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Saídas
As saídas (encadernações `direction === "out"` de) podem ser escritas por uma função de várias maneiras. Em todos os casos, a `name` propriedade da vinculação definida em *function.js* corresponde ao nome do membro do objeto escrito na sua função. 

Pode atribuir dados a encadernações de saída de uma das seguintes formas (não combine estes métodos):

- **_[Recomendado para múltiplas saídas]_ Devolvendo um objeto.** Se estiver a utilizar uma função de retorno assínc/Promessa, pode devolver um objeto com dados de saída atribuídos. No exemplo abaixo, as ligações de saída são denominada "httpResponse" e "queueOutput" em *function.jsem* .

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

  Se estiver a utilizar uma função sincronizada, pode devolver este objeto utilizando [`context.done`](#contextdone-method) (ver exemplo).
- **_[Recomendado para uma única saída]_ Devolvendo um valor diretamente e usando o nome de ligação $return.** Isto só funciona para funções de retorno assync/Promise. Veja o exemplo na [exportação de uma função assídua](#exporting-an-async-function). 
- **Atribuindo valores a `context.bindings`** Pode atribuir valores diretamente a contexto.encadernações.

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

### <a name="bindings-data-type"></a>Tipo de dados de encadernação

Para definir o tipo de dados para uma ligação de entrada, utilize o `dataType` imóvel na definição de encadernação. Por exemplo, para ler o conteúdo de um pedido HTTP em formato binário, utilize o tipo `binary` :

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

As opções para `dataType` são: `binary` `stream` `string` e.

## <a name="context-object"></a>objeto de contexto

O tempo de execução utiliza um `context` objeto para transmitir dados de e para a sua função e o tempo de execução. Usado para ler e definir dados de encadernações e para escrever para registos, o `context` objeto é sempre o primeiro parâmetro passado para uma função.

Para funções com código sincronizado, o objeto de contexto inclui a `done` chamada de chamada a que chama quando a função é feita de processamento. Explicitamente chamar `done` é desnecessário ao escrever código assíncrona; o retorno é chamado `done` implicitamente.

```javascript
module.exports = (context) => {

    // function logic goes here

    context.log("The function has executed.");

    context.done();
};
```

O contexto passado para a sua função expõe uma `executionContext` propriedade, que é um objeto com as seguintes propriedades:

| Nome da propriedade  | Tipo  | Description |
|---------|---------|---------|
| `invocationId` | String | Fornece um identificador único para a invocação de funções específicas. |
| `functionName` | String | Fornece o nome da função de execução |
| `functionDirectory` | String | Fornece o diretório de aplicações de funções. |

O exemplo que se segue mostra como devolver o `invocationId` .

```javascript
module.exports = (context, req) => {
    context.res = {
        body: context.executionContext.invocationId
    };
    context.done();
};
```

### <a name="contextbindings-property"></a>contexto.imóvel de encadernações

```js
context.bindings
```

Devolve um objeto nomeado que é usado para ler ou atribuir dados de ligação. Os dados de entrada e de ligação do gatilho podem ser acedidos através da leitura de propriedades em `context.bindings` . Os dados vinculativos da saída podem ser atribuídos adicionando dados a `context.bindings`

Por exemplo, as seguintes definições vinculativas no seu function.jsem permitir-lhe aceder ao conteúdo de uma fila `context.bindings.myInput` e atribuir saídas a uma fila utilizando `context.bindings.myOutput` .

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

Pode optar por definir dados de ligação de saída utilizando o `context.done` método em vez do objeto `context.binding` (ver abaixo).

### <a name="contextbindingdata-property"></a>context.bindingData propriedade

```js
context.bindingData
```

Devolve um objeto nomeado que contém metadados de gatilho e dados de invocação de função `invocationId` (, `sys.methodName` , , , `sys.utcNow` `sys.randGuid` . Para um exemplo de metadados de gatilho, consulte este [exemplo dos centros de eventos](functions-bindings-event-hubs-trigger.md).

### <a name="contextdone-method"></a>método de contexto.feito

```js
context.done([err],[propertyBag])
```

Informe o tempo de execução de que o seu código está concluído. Quando a sua função utiliza a [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) declaração, não precisa de ser utilizada `context.done()` . A `context.done` chamada é implicitamente chamada. As funções async estão disponíveis no Nó 8 ou numa versão posterior, que requer a versão 2.x do tempo de execução das Funções.

Se a sua função não for uma função assínc, **deve ligar** para informar o tempo `context.done` de funcionamento de que a sua função está completa. A execução é o caso de faltar.

O `context.done` método permite-lhe repercutir tanto um erro definido pelo utilizador para o tempo de execução como um objeto JSON que contém dados de ligação de saída. As propriedades passaram para `context.done` substituir qualquer coisa definida no `context.bindings` objeto.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>método context.log  

```js
context.log(message)
```

Permite-lhe escrever para os registos de funções de streaming no nível de rastreação predefinido, com outros níveis de registo disponíveis. A registo de vestígios é descrita em detalhe na secção seguinte. 

## <a name="write-trace-output-to-logs"></a>Escrever saída de vestígios para registos

Em Funções, utiliza os `context.log` métodos para escrever vestígios de saída para os registos e para a consola. Quando `context.log()` liga, a sua mensagem é escrita para os registos no nível de rastreação predefinido, que é o nível de rastreio de _informações._ As funções integram-se com o Azure Application Insights para capturar melhor os registos de aplicações da sua função. A Application Insights, parte do Azure Monitor, fornece instalações para recolha, renderização visual e análise tanto da telemetria da aplicação como das suas saídas de vestígios. Para saber mais, consulte [a monitorização das Funções Azure](functions-monitoring.md).

O exemplo a seguir escreve um registo ao nível do rastreio de informações, incluindo o ID de invocação:

```javascript
context.log("Something has happened. " + context.invocationId); 
```

Todos os `context.log` métodos suportam o mesmo formato de parâmetro que é suportado pelo método Node.js [util.formato](https://nodejs.org/api/util.html#util_util_format_format). Considere o seguinte código, que escreve registos de funções utilizando o nível de rastreação predefinido:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Também pode escrever o mesmo código no seguinte formato:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

> [!NOTE]  
> Não `console.log` escreva vestígios. Como a saída `console.log` é capturada ao nível da aplicação de função, não está ligada a uma invocação de função específica e não é exibida nos registos de uma função específica. Além disso, a versão 1.x do tempo de execução de Funções não suporta usar `console.log` para escrever para a consola.

### <a name="trace-levels"></a>Níveis de vestígios

Além do nível predefinido, estão disponíveis os seguintes métodos de registo que permitem escrever registos de funções em níveis específicos de rastreamento.

| Método                 | Descrição                                |
| ---------------------- | ------------------------------------------ |
| **erro _(mensagem)_**   | Escreve um evento de nível de erro nos registos.   |
| **alertar _(mensagem)_**    | Escreve um evento de nível de aviso para os registos. |
| **informação _(mensagem)_**    | Escreve para registo de nível de informação ou menor.    |
| **verbose ( _mensagem)_** | Escreve para a gravação de nível verboso.           |

O exemplo a seguir escreve o mesmo registo ao nível dos rastreios de aviso, em vez do nível de informação:

```javascript
context.log.warn("Something has happened. " + context.invocationId); 
```

Como o _erro_ é o nível mais alto de rastreá-lo, este traço é escrito para a saída em todos os níveis de rastrear, desde que a exploração madeireira esteja ativada.

### <a name="configure-the-trace-level-for-logging"></a>Configure o nível de vestígios para a exploração madeireira

As funções permitem definir o nível de traço de limiar para escrever nos registos ou na consola. As definições de limiar específicas dependem da sua versão do tempo de execução de Funções.

# <a name="v2x"></a>[v2.x+](#tab/v2)

Para definir o limiar para vestígios escritos nos registos, utilize a `logging.logLevel` propriedade na host.jsem arquivo. Este objeto JSON permite definir um limiar padrão para todas as funções na sua aplicação de funções, além de que pode definir limiares específicos para funções individuais. Para saber mais, consulte [Como configurar a monitorização para as Funções Azure](configure-monitoring.md).

# <a name="v1x"></a>[v1.x](#tab/v1)

Para definir o limiar para todos os vestígios escritos para registos e consola, utilize a `tracing.consoleLevel` propriedade no host.jsem arquivo. Esta definição aplica-se a todas as funções da sua aplicação de funções. O exemplo a seguir define o limiar de rastreio para permitir a exploração de verbose:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Valores de **consolaLevel** corresponde aos nomes dos `context.log` métodos. Para desativar todos os vestígios de registo da consola, desative a **consolaLevel** para _desligar_ . Para obter mais informações, consulte [host.jsna referência v1.x](functions-host-json-v1.md).

---

### <a name="log-custom-telemetry"></a>Registar telemetria personalizada

Por predefinição, as funções escrevem a saída como traços para Insights de Aplicação. Para mais controlo, pode, em vez disso, utilizar o [Application Insights Node.js SDK](https://github.com/microsoft/applicationinsights-node.js) para enviar dados de telemetria personalizados para a sua instância De Insights de Aplicação. 

# <a name="v2x"></a>[v2.x+](#tab/v2)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.traceContext.traceparent};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

# <a name="v1x"></a>[v1.x](#tab/v1)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.operationId};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

---

O `tagOverrides` parâmetro define o `operation_Id` ID de invocação da função. Esta definição permite-lhe correlacionar toda a telemetria gerada e personalizada automaticamente para uma determinada invocação de função.

## <a name="http-triggers-and-bindings"></a>Disparadores e encadernações HTTP

Os gatilhos HTTP e Webhook e as ligações de saída HTTP utilizam objetos de pedido e resposta para representar as mensagens HTTP.  

### <a name="request-object"></a>Objeto de pedido

O `context.req` (pedido) tem as seguintes propriedades:

| Propriedade      | Descrição                                                    |
| ------------- | -------------------------------------------------------------- |
| _corpo_        | Um objeto que contém o corpo do pedido.               |
| _cabeçalhos_     | Um objeto que contém os cabeçalhos de pedido.                   |
| _método_      | O método HTTP do pedido.                                |
| _originalUrl_ | A URL do pedido.                                        |
| _params_      | Um objeto que contém os parâmetros de encaminhamento do pedido. |
| _consulta_       | Um objeto que contém os parâmetros de consulta.                  |
| _rawBody_     | O corpo da mensagem como uma corda.                           |


### <a name="response-object"></a>Objeto de resposta

O `context.res` objeto (resposta) tem as seguintes propriedades:

| Propriedade  | Descrição                                               |
| --------- | --------------------------------------------------------- |
| _corpo_    | Um objeto que contém o corpo da resposta.         |
| _cabeçalhos_ | Um objeto que contém os cabeçalhos de resposta.             |
| _isRaw_   | Indica que a formatação é ignorada para a resposta.    |
| _estado_  | O código de estado HTTP da resposta.                     |
| _cookies_ | Uma matriz de objetos de cookies HTTP que são definidos na resposta. Um objeto de cookie HTTP tem `name` `value` um, e outras propriedades de cookies, tais como `maxAge` ou `sameSite` . |

### <a name="accessing-the-request-and-response"></a>Acede ao pedido e resposta 

Quando trabalha com os detonadores HTTP, pode aceder aos objetos de pedido e resposta HTTP de várias formas:

+ **De `req` e propriedades no `res` `context` objeto.** Desta forma, pode utilizar o padrão convencional para aceder aos dados HTTP a partir do objeto de contexto, em vez de ter de usar o `context.bindings.name` padrão completo. O exemplo a seguir mostra como aceder aos `req` `res` e objetos `context` no:

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **A partir dos encadernações de entrada e saída nomeados.** Desta forma, o gatilho HTTP e as ligações funcionam da mesma forma que qualquer outra ligação. O exemplo a seguir define o objeto de resposta utilizando uma ligação com o `response` nome: 

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
+ **_[Apenas resposta]_ `context.res.send(body?: any)`Chamando.** Uma resposta HTTP é criada com a entrada `body` como o corpo de resposta. `context.done()` é implicitamente chamado.

+ **_[Apenas resposta]_ `context.done()`Chamando.** Um tipo especial de ligação HTTP devolve a resposta que é passada ao `context.done()` método. A seguinte ligação de saída HTTP define um `$return` parâmetro de saída:

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

## <a name="scaling-and-concurrency"></a>Escala e concuência

Por predefinição, o Azure Functions monitoriza automaticamente a carga da sua aplicação e cria casos adicionais de anfitrião para Node.js conforme necessário. As funções utilizam limiares incorporados (não configuráveis pelo utilizador) para diferentes tipos de gatilhos para decidir quando adicionar instâncias, como a idade das mensagens e o tamanho da fila para o QueueTrigger. Para mais informações, consulte [como funcionam os planos De Consumo e Premium.](functions-scale.md#how-the-consumption-and-premium-plans-work)

Este comportamento de escala é suficiente para muitas aplicações Node.js. Para aplicações com ligação ao CPU, pode melhorar ainda mais o desempenho utilizando vários processos de trabalho linguístico.

Por padrão, cada instância de anfitrião de Funções tem um único processo de trabalho de língua. Pode aumentar o número de processos de trabalhador por hospedeiro (até 10) utilizando a definição de aplicação [FUNCTIONS_WORKER_PROCESS_COUNT.](functions-app-settings.md#functions_worker_process_count) As Funções Azure tentam então distribuir uniformemente invocações de funções simultâneas por estes trabalhadores. 

O FUNCTIONS_WORKER_PROCESS_COUNT aplica-se a cada anfitrião que as Funções criam ao escalonar a sua aplicação para satisfazer a procura. 

## <a name="node-version"></a>Versão nó

A tabela a seguir mostra as versões Node.js suportadas atuais para cada versão principal do tempo de execução das Funções, pelo sistema operativo:

| Versão de funções | Versão nó (Windows) | Versão nó (Linux) |
|---|---| --- |
| 1.x | 6.11.2 (travado pelo tempo de funcionação) | n/a |
| 2.x  | ~8<br/>~10 (recomendado)<br/>~12<sup>*</sup> | ~8 (recomendado)<br/>~10  |
| 3.x | ~10<br/>~12 (recomendado)  | ~10<br/>~12 (recomendado) |

<sup>*</sup>O nó ~12 é atualmente permitido na versão 2.x do tempo de execução das Funções. No entanto, para melhor desempenho, recomendamos a utilização da versão 3.x do tempo de execução das funções com o Nó ~12. 

Pode ver a versão atual que o tempo de execução está a utilizar, verificando a definição da aplicação acima ou imprimindo `process.version` a partir de qualquer função. Direcione a versão em Azure definindo a definição de [WEBSITE_NODE_DEFAULT_VERSION aplicação](functions-how-to-use-azure-function-app-settings.md#settings) para uma versão LTS suportada, como `~10` .

## <a name="dependency-management"></a>Gestão de dependências
Para utilizar bibliotecas comunitárias no seu código JavaScript, como é mostrado no exemplo abaixo, é necessário garantir que todas as dependências estão instaladas na sua App de Função em Azure.

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
> Deve definir um `package.json` ficheiro na raiz da sua App de Função. A definição do ficheiro permite que todas as funções da app partilhem os mesmos pacotes em cache, o que dá o melhor desempenho. Se surgir um conflito de versão, pode resolvê-lo adicionando um `package.json` ficheiro na pasta de uma função específica.  

Ao implementar As Aplicações de Função a partir do controlo de origem, qualquer `package.json` ficheiro presente no seu repo, irá ativar uma pasta durante a `npm install` implementação. Mas ao ser implantado através do Portal ou do CLI, terá de instalar manualmente as embalagens.

Existem duas formas de instalar pacotes na sua App de Função: 

### <a name="deploying-with-dependencies"></a>Implantação com Dependências
1. Instale todos os pacotes necessários localmente, funcionando `npm install` .

2. Desloque o seu código e certifique-se de que a `node_modules` pasta está incluída na implementação. 


### <a name="using-kudu"></a>Usando Kudu
1. Aceda a `https://<function_app_name>.scm.azurewebsites.net`.

2. Clique **em Debug Console**  >  **CMD** .

3. Vá para `D:\home\site\wwwroot` , e em seguida, arraste o seu package.jsno ficheiro para a pasta **wwwroot** na metade superior da página.  
    Também pode fazer o upload de ficheiros para a sua aplicação de função de outras formas. Para obter mais informações, consulte [Como atualizar ficheiros de aplicações de função.](functions-reference.md#fileupdate) 

4. Depois de o package.jsno ficheiro ser carregado, executa o `npm install` comando na consola de **execução remota kudu** .  
    Esta ação descarrega os pacotes indicados no package.jsno ficheiro e reinicia a aplicação de função.

## <a name="environment-variables"></a>Variáveis de ambiente

Em Funções, [as configurações da aplicação](functions-app-settings.md), como as cadeias de ligação de serviço, são expostas como variáveis ambientais durante a execução. Pode aceder a estas definições `process.env` utilizando, como mostrado aqui na segunda e terceira chamadas para `context.log()` onde registamos as `AzureWebJobsStorage` variáveis e `WEBSITE_SITE_NAME` ambientais:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Ao correr localmente, as definições de aplicações são lidas a partir do [local.settings.jsno](functions-run-local.md#local-settings-file) ficheiro do projeto.

## <a name="configure-function-entry-point"></a>Configure ponto de entrada de função

As `function.json` propriedades e podem ser `scriptFile` `entryPoint` usadas para configurar a localização e o nome da sua função exportada. Estas propriedades podem ser importantes quando o seu JavaScript é transposto.

### <a name="using-scriptfile"></a>Ao utilizar `scriptFile`

Por predefinição, uma função JavaScript é executada a partir de `index.js` , um ficheiro que partilha o mesmo directório-mãe que o correspondente `function.json` .

`scriptFile` pode ser usado para obter uma estrutura de pasta que se pareça com o seguinte exemplo:

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

O `function.json` for deve incluir uma propriedade que indique o ficheiro com a `myNodeFunction` `scriptFile` função exportada a executar.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Ao utilizar `entryPoint`

Em `scriptFile` (ou `index.js` ), uma função deve ser exportada usando `module.exports` para ser encontrada e executada. Por predefinição, a função que executa quando desencadeada é a única exportação desse ficheiro, a exportação denominada `run` , ou a exportação denominada `index` .

Isto pode ser configurado usando `entryPoint` em , como no exemplo `function.json` seguinte:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

Nas Funções v2.x, que suporta o `this` parâmetro nas funções do utilizador, o código de função pode então ser como no exemplo seguinte:

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

Neste exemplo, é importante notar que, embora um objeto esteja a ser exportado, não existem garantias para preservar o Estado entre execuções.

## <a name="local-debugging"></a>Local Debugging

Quando iniciado com o `--inspect` parâmetro, um processo Node.js ouve um cliente depuração na porta especificada. Nas Funções Azure 2.x, pode especificar argumentos para passar para o processo Node.js que executa o seu código adicionando a variável ambiental ou a Definição de Aplicações `languageWorkers:node:arguments = <args>` . 

Para depurar localmente, adicione `"languageWorkers:node:arguments": "--inspect=5858"` `Values` abaixo o seu [local.settings.jsem](./functions-run-local.md#local-settings-file) arquivo e anexe um depurar à porta 5858.

Ao depurar usando o Código VS, o `--inspect` parâmetro é automaticamente adicionado usando o valor na launch.jsdo projeto em `port` arquivo.

Na versão 1.x, a definição `languageWorkers:node:arguments` não funcionará. A porta de depuração pode ser selecionada com o [`--nodeDebugPort`](./functions-run-local.md#start) parâmetro em Azure Functions Core Tools.

## <a name="typescript"></a>TypeScript

Quando se destina à versão 2.x do tempo de execução de Funções, tanto as [funções Azure para o Código do Estúdio Visual](functions-create-first-function-vs-code.md) como as [Ferramentas Principais de Funções Azure](functions-run-local.md) permitem criar aplicações de função utilizando um modelo que suporta projetos de aplicações de funções TypeScript. O modelo gera `package.json` e `tsconfig.json` projeta ficheiros que facilitam a transpilagem, execução e publicação de funções JavaScript a partir do código TypeScript com estas ferramentas.

Um ficheiro gerado `.funcignore` é utilizado para indicar quais os ficheiros que são excluídos quando um projeto é publicado no Azure.  

Os ficheiros TypeScript (.ts) são transpostos em ficheiros JavaScript (.js) no `dist` diretório de saída. Os modelos TypeScript utilizam o [ `scriptFile` parâmetro](#using-scriptfile) `function.json` para indicar a localização do ficheiro .js correspondente na `dist` pasta. A localização de saída é definida pelo modelo utilizando `outDir` o parâmetro no `tsconfig.json` ficheiro. Se alterar esta definição ou o nome da pasta, o tempo de execução não é capaz de encontrar o código a executar.

A forma como desenvolve e implementa localmente a partir de um projeto TypeScript depende da sua ferramenta de desenvolvimento.

### <a name="visual-studio-code"></a>Visual Studio Code

As [funções Azure para](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) a extensão do Código do Estúdio Visual permitem desenvolver as suas funções utilizando o TypeScript. As Ferramentas Centrais são um requisito da extensão de Funções Azure.

Para criar uma aplicação de função TypeScript no Código do Estúdio Visual, escolha `TypeScript` como seu idioma quando criar uma aplicação de função.

Quando pressiona **f5** para executar a aplicação localmente, a transpilação é feita antes do anfitrião (func.exe) ser inicializado. 

Quando implementa a sua aplicação de função para Azure utilizando o botão **Implementar para funcionar...** botão, a extensão Azure Functions gera primeiro uma construção pronta a produção de ficheiros JavaScript a partir dos ficheiros de origem TypeScript.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Existem várias formas de um projeto TypeScript diferir de um projeto JavaScript ao utilizar as Ferramentas Principais.

#### <a name="create-project"></a>Criar o projeto

Para criar um projeto de aplicação de função TypeScript utilizando Ferramentas Core, tem de especificar a opção de idioma TypeScript quando criar a sua aplicação de função. Pode fazê-lo de uma das seguintes formas:

- Executar o `func init` comando, selecione `node` como pilha de idiomas e, em seguida, selecione `typescript` .

- Execute o comando `func init --worker-runtime typescript`.

#### <a name="run-local"></a>Executar local

Para executar o código de aplicação de função localmente utilizando Ferramentas Core, utilize os seguintes comandos em vez `func host start` de: 

```command
npm install
npm start
```

O `npm start` comando é equivalente aos seguintes comandos:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Publicar no Azure

Antes de utilizar o [`func azure functionapp publish`] comando para implantar no Azure, cria uma construção pronta para a produção de ficheiros JavaScript a partir dos ficheiros de origem TypeScript. 

Os seguintes comandos preparam e publicam o seu projeto TypeScript utilizando Ferramentas Core: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

Neste comando, `<APP_NAME>` substitua-o pelo nome da sua aplicação de função.

## <a name="considerations-for-javascript-functions"></a>Considerações para funções JavaScript

Quando trabalhar com funções JavaScript, esteja ciente das considerações nas seguintes secções.

### <a name="choose-single-vcpu-app-service-plans"></a>Escolha planos de serviço de aplicações single-vCPU

Quando cria uma aplicação de função que utiliza o plano de Serviço de Aplicações, recomendamos que selecione um plano de um único vCPU em vez de um plano com vários vCPUs. Hoje em dia, as funções javaScript funcionam de forma mais eficiente em VMs single-vCPU, e usar VMs maiores não produz as melhorias de desempenho esperadas. Quando necessário, pode escalar manualmente adicionando mais instâncias VM single-vCPU, ou pode ativar a autoescala. Para obter mais informações, consulte [a contagem de instâncias de escala manual ou automática.](../azure-monitor/platform/autoscale-get-started.md?toc=/azure/app-service/toc.json)

### <a name="cold-start"></a>Início a Frio

Ao desenvolver funções Azure no modelo de hospedagem sem servidor, o frio começa a ser uma realidade. *O arranque* a frio refere-se ao facto de que quando a aplicação de função começa pela primeira vez após um período de inatividade, demora mais tempo a arrancar. Para funções JavaScript com árvores de grande dependência em particular, o arranque a frio pode ser significativo. Para acelerar o processo de arranque a frio, [executar as suas funções como um ficheiro de embalagem,](run-functions-from-deployment-package.md) quando possível. Muitos métodos de implementação usam a corrida do modelo de pacote por padrão, mas se você estiver experimentando grandes arranques frios e não estiver funcionando desta forma, esta mudança pode oferecer uma melhoria significativa.

### <a name="connection-limits"></a>Limites de ligação

Quando utilizar um cliente específico de serviço numa aplicação Azure Functions, não crie um novo cliente com cada invocação de funções. Em vez disso, crie um único cliente estático no âmbito global. Para obter mais informações, consulte [a gestão de ligações em Funções Azure](manage-connections.md).

### <a name="use-async-and-await"></a>Uso `async` e `await`

Ao escrever Funções Azure em JavaScript, deve escrever código utilizando as `async` `await` palavras-chave e as palavras-chave. Escrever códigos usando `async` e em vez de `await` callbacks ou `.then` com `.catch` Promessas ajuda a evitar dois problemas comuns:
 - Lançar exceções sem escoadas que [colidam com o processo Node.js,](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly)podendo afetar a execução de outras funções.
 - Comportamento inesperado, como registos em falta do context.log, causado por chamadas assíncronos que não são devidamente aguardados.

No exemplo abaixo, o método assíncronos `fs.readFile` é invocado com uma função de retorno de erro primeiro como segundo parâmetro. Este código causa ambas as questões acima mencionadas. Uma exceção que não é explicitamente apanhada no âmbito correto que interrompeu todo o processo (questão #1). Chamar `context.done()` para fora do âmbito da função de retorno significa que a invocação da função pode terminar antes da leitura do ficheiro (emissão #2). Neste exemplo, chamar `context.done()` muito cedo resulta em entradas de registo em falta a partir de `Data from file:` .

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

A utilização das `async` `await` palavras-chave e das palavras-chave ajuda a evitar ambos estes erros. Deve utilizar a função de utilitário Node.js [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) para transformar funções de estilo de chamada de erro primeiro em funções aguardaveis.

No exemplo abaixo, quaisquer exceções não manipuladas lançadas durante a execução da função apenas falham na invocação individual que levantou uma exceção. A `await` palavra-chave significa que os passos que seguem `readFileAsync` apenas executam depois `readFile` de concluídos. Com `async` `await` e, você também não precisa ligar para o `context.done()` retorno.

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

Para obter mais informações, veja os seguintes recursos:

+ [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
+ [Referência para programadores das Funções do Azure](functions-reference.md)
+ [Funções Azure dispara e encaderna](functions-triggers-bindings.md)

['func azure functionapp publicar']: functions-run-local.md#project-file-deployment
