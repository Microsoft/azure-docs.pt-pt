---
title: Referência do desenvolvedor JavaScript para funções azure
description: Compreenda como desenvolver funções utilizando o JavaScript.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: reference
ms.date: 12/17/2019
ms.openlocfilehash: 345df8e1ea88caa6f8dbe941245c1f989c3e81c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276832"
---
# <a name="azure-functions-javascript-developer-guide"></a>Guia de desenvolvedores de funções azure JavaScript

Este guia contém informações sobre as complexidades da escrita de Funções Azure com JavaScript.

Uma função JavaScript `function` é uma exportação que executa quando ativada[(os gatilhos são configurados em função.json](functions-triggers-bindings.md)). O primeiro argumento passado a `context` todas as funções é um objeto, que é usado para receber e enviar dados vinculativos, registar e comunicar com o tempo de execução.

Este artigo assume que já leu a referência do desenvolvedor de [Funções Azure](functions-reference.md). Complete as Funções rapidamente para criar a sua primeira função, utilizando o Código do [Estúdio Visual](functions-create-first-function-vs-code.md) ou [no portal](functions-create-first-azure-function.md).

Este artigo também suporta o desenvolvimento de [aplicações TypeScript](#typescript).

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

Na raiz do projeto, há um ficheiro [host.json](functions-host-json.md) partilhado que pode ser usado para configurar a aplicação de função. Cada função tem uma pasta com o seu próprio ficheiro de código (.js) e ficheiro de configuração de ligação (função.json). O nome `function.json`do diretório dos pais é sempre o nome da sua função.

As extensões de encadernação exigidas na [versão 2.x](functions-versions.md) do tempo de execução das Funções são definidas no `extensions.csproj` ficheiro, com os ficheiros reais da biblioteca na `bin` pasta. Ao desenvolver-se localmente, deve [registar extensões vinculativas](./functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal Azure, este registo é feito para si.

## <a name="exporting-a-function"></a>Exportação de uma função

As funções JavaScript devem [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) ser [`exports`](https://nodejs.org/api/modules.html#modules_exports)exportadas através (ou ). A sua função exportada deve ser uma função JavaScript que executa quando desencadeada.

Por predefinição, o tempo de `index.js`funcionamento `index.js` das Funções procura a `function.json`sua função em , onde partilha o mesmo directório-mãe que o correspondente . No caso de incumprimento, a sua função exportada deve `run` `index`ser a única exportação do seu ficheiro ou da exportação nomeada ou . Para configurar a localização do ficheiro e o nome de exportação da sua função, leia sobre [a configuração do ponto de entrada da sua função](functions-reference-node.md#configure-function-entry-point) abaixo.

A sua função exportada é aprovada uma série de argumentos sobre a execução. O primeiro argumento é `context` sempre um objeto. Se a sua função for sincronizada (não devolve uma `context` Promessa), deve `context.done` passar o objeto, uma vez que é necessária uma chamada para uma utilização correta.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Exportação de uma função de assinos
Ao utilizar [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) a declaração ou as [promessas](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) simples do JavaScript na versão 2.x do [`context.done`](#contextdone-method) tempo de funcionamento das Funções, não precisa de ligar explicitamente para a chamada para sinalizar que a sua função está concluída. A sua função completa quando a função de asincronização exportada/Promessa termina. Para funções que visem o tempo de execução da versão 1.x, ainda deve ligar [`context.done`](#contextdone-method) quando o seu código terminar a execução.

O exemplo seguinte é uma função simples que regista que foi desencadeada e completa imediatamente a execução.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Ao exportar uma função de asincronização, também pode `return` configurar uma ligação de saída para obter o valor. Isto é recomendado se tiver apenas uma ligação de saída.

Para atribuir uma `return`saída utilizando, altere a `name` propriedade para `$return` dentro `function.json`.

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
No JavaScript, as [ligações](functions-triggers-bindings.md) são configuradas e definidas na função de uma função.json. As funções interagem com ligações de várias maneiras.

### <a name="inputs"></a>Entradas
A entrada é dividida em duas categorias em Funções Azure: uma é a entrada do gatilho e a outra é a entrada adicional. O gatilho e outras encadernações de entrada (encadernações de) `direction === "in"`podem ser lidas por uma função de três maneiras:
 - **_[Recomendado]_ À medida que os parâmetros passavam para a sua função.** São passados para a função na mesma ordem que são definidos em *função.json*. A `name` propriedade definida em *função.json* não precisa de corresponder ao nome do seu parâmetro, embora deva.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Como membros [`context.bindings`](#contextbindings-property) do objeto.** Cada membro é `name` nomeado pela propriedade definida em *função.json*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Como entradas usando o [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) objeto JavaScript.** Isto é essencialmente o mesmo que passar inputs como parâmetros, mas permite-lhe lidar com as inputs dinamicamente.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Saídas
As saídas (encadernações de) `direction === "out"`podem ser escritas por uma função de várias maneiras. Em todos os `name` casos, a propriedade da encadernação tal como definida na *função.json* corresponde ao nome do membro do objeto escrito na sua função. 

Pode atribuir dados a encadernações de saída de uma das seguintes formas (não combine estes métodos):

- **_[Recomendado para várias saídas]_ Devolver um objeto.** Se estiver a utilizar uma função de devolução de sincronização/promessa, pode devolver um objeto com os dados de saída atribuídos. No exemplo abaixo, as encadernações de saída são denominadas "httpResponse" e "queueOutput" em *função.json*.

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

  Se estiver a utilizar uma função sincronizada, pode [`context.done`](#contextdone-method) devolver este objeto utilizando (ver exemplo).
- **_[Recomendado para uma saída única]_ Devolvendo um valor diretamente e utilizando o $return nome de ligação.** Isto funciona apenas para funções de retorno de sincronia/promessa. Consulte o exemplo na [exportação de uma função de asincronização](#exporting-an-async-function). 
- **Atribuindo valores a `context.bindings` ** Pode atribuir valores diretamente ao contexto.encadernações.

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

Para definir o tipo de dados para `dataType` uma ligação de entrada, utilize a propriedade na definição de encadernação. Por exemplo, para ler o conteúdo de um pedido `binary`HTTP em formato binário, utilize o tipo:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

As `dataType` opções `binary` `stream`para `string`são: , e .

## <a name="context-object"></a>objeto de contexto
O tempo de `context` execução utiliza um objeto para passar dados de e para a sua função e para permitir que se comunique com o tempo de funcionamento. O objeto de contexto pode ser usado para ler e definir `context.done` dados a partir de encadernações, registos de escrita e utilização do backback quando a sua função exportada é sincronizada.

O `context` objeto é sempre o primeiro parâmetro para uma função. Deve ser incluído porque tem métodos `context.done` `context.log`importantes como e . Pode nomear o objeto o que quiser `ctx` `c`(por exemplo, ou).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>contexto.bindings propriedade

```js
context.bindings
```

Devolve um objeto nomeado que é usado para ler ou atribuir dados de ligação. Os dados de entrada e de encadernação podem ser acedidos através da leitura de propriedades em `context.bindings`. Dados de ligação de saída podem ser atribuídos adicionando dados`context.bindings`

Por exemplo, as seguintes definições de ligação na sua função.json permitem-lhe aceder ao conteúdo de uma fila a partir de `context.bindings.myInput` e atribuir saídas a uma fila utilizando `context.bindings.myOutput`.

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

Pode optar por definir dados `context.done` de ligação `context.binding` de saída utilizando o método em vez do objeto (ver abaixo).

### <a name="contextbindingdata-property"></a>contexto.bindingPropriedadeData

```js
context.bindingData
```

Devolve um objeto nomeado que contém metadados`invocationId`de `sys.methodName` `sys.utcNow`gatilho `sys.randGuid`e dados de invocação de funções ( , , , ). Para um exemplo de metadados de gatilho, consulte este [evento hubs exemplo](functions-bindings-event-hubs-trigger.md).

### <a name="contextdone-method"></a>método de contexto.feito

```js
context.done([err],[propertyBag])
```

Permite que o tempo de execução saiba que o seu código está completo. Quando a sua [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) função utiliza a `context.done()`declaração, não precisa de ser utilizada . A `context.done` chamada é implicitamente chamada. As funções de async estão disponíveis no nó 8 ou numa versão posterior, que requer a versão 2.x do tempo de execução das funções.

Se a sua função não for uma função de asincronização, **deve ligar** `context.done` para informar o tempo de funcionamento de que a sua função está completa. A execução é a hora se faltar.

O `context.done` método permite-lhe passar de volta um erro definido pelo utilizador para o tempo de execução e um objeto JSON contendo dados de ligação de saída. As propriedades `context.done` passaram para substituir `context.bindings` qualquer coisa definida no objeto.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>método de contexto.log  

```js
context.log(message)
```

Permite-lhe escrever para os registos da função de streaming ao nível de rastreio predefinido. Em `context.log`, existem métodos adicionais de exploração florestal que permitem escrever registos de funções noutros níveis de vestígios:


| Método                 | Descrição                                |
| ---------------------- | ------------------------------------------ |
| **_erro(mensagem)_**   | Escreve para o nível de erro de registo, ou inferior.   |
| **aviso(_mensagem_)**    | Escreve para o nível de alerta de exploração madeireira, ou inferior. |
| **informação(_mensagem)_**    | Escreve para registo de nível de informação, ou menor.    |
| **verboso(_mensagem)_** | Escreve para verboso nível de exploração.           |

O seguinte exemplo escreve um registo ao nível do rastreio de advertência:

```javascript
context.log.warn("Something has happened."); 
```

Pode [configurar o limiar de nível de rastreio para a exploração](#configure-the-trace-level-for-console-logging) de registo no ficheiro host.json. Para obter mais informações sobre a escrita de registos, consulte a [escrita de vestígios](#writing-trace-output-to-the-console) abaixo.

Leia a monitorização das [Funções Azure](functions-monitoring.md) para saber mais sobre os registos de funções de visualização e consulta.

## <a name="writing-trace-output-to-the-console"></a>Escrita de vestígios de saída para a consola 

Em Funções, utiliza `context.log` os métodos para escrever a saída de vestígios para a consola. Nas funções v2.x, as `console.log` saídas de vestígios que utilizam são capturadas ao nível da App de Funções. Isto significa que `console.log` as saídas de não estão ligadas a uma invocação de função específica e não são exibidas nos registos de uma função específica. No entanto, propagam-se à Aplicação Insights. Em Funções v1.x, `console.log` não pode supor escrever para a consola.

Quando ligar, `context.log()`a sua mensagem é escrita para a consola ao nível de traço padrão, que é o nível de rastreio da _informação._ O seguinte código escreve para a consola ao nível do rastreio da informação:

```javascript
context.log({hello: 'world'});  
```

Este código é equivalente ao código acima:

```javascript
context.log.info({hello: 'world'});  
```

Este código escreve para a consola ao nível de erro:

```javascript
context.log.error("An error has occurred.");  
```

Como o _erro_ é o nível mais alto de rastreio, este traço é escrito para a saída em todos os níveis de vestígios, desde que o registo esteja ativado.

Todos `context.log` os métodos suportam o mesmo formato de parâmetro que é suportado pelo método de [formato](https://nodejs.org/api/util.html#util_util_format_format)node.js . Considere o seguinte código, que escreve registos de funções utilizando o nível de traço predefinido:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Também pode escrever o mesmo código no seguinte formato:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Configure o nível de rastreio para o registo de consolas

As funções 1.x permitem definir o nível de traço limiar para a escrita na consola, o que facilita o controlo da forma como os vestígios são escritos para a consola a partir da sua função. Para definir o limiar para todos os vestígios `tracing.consoleLevel` escritos na consola, utilize a propriedade no ficheiro host.json. Esta definição aplica-se a todas as funções da sua aplicação de funções. O exemplo que se segue define o limiar do vestígio para permitir a exploração verbosa:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Os valores das **consolasNcorrespondem** aos nomes dos `context.log` métodos. Para desativar todos os vestígios de registo da consola, desative o **nível da consola** para _desligar_. Para mais informações, consulte [host.json reference](functions-host-json-v1.md).

## <a name="http-triggers-and-bindings"></a>HTTP gatilhos e encadernações

OS gatilhos HTTP e webhook e as encadernações de saída HTTP utilizam objetos de pedido e resposta para representar as mensagens HTTP.  

### <a name="request-object"></a>Objeto de pedido

O `context.req` objeto (pedido) tem as seguintes propriedades:

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
| _status_  | O código de estado HTTP da resposta.                     |
| _cookies_ | Uma variedade de objetos de cookie HTTP que são definidos na resposta. Um objeto de `name`cookie `value`HTTP tem uma, `maxAge` e `sameSite`outras propriedades de cookies, tais como ou . |

### <a name="accessing-the-request-and-response"></a>Acesso ao pedido e resposta 

Quando trabalha com os gatilhos HTTP, pode aceder aos objetos de pedido e resposta http de várias maneiras:

+ **De `req` `res` e propriedades `context` no objeto.** Desta forma, pode utilizar o padrão convencional para aceder aos dados HTTP do `context.bindings.name` objeto de contexto, em vez de ter de usar o padrão completo. O exemplo que se `req` segue `res` mostra `context`como aceder aos e objetos do :

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Das ligações de entrada e saída nomeadas.** Desta forma, o gatilho HTTP e as encadernações funcionam da mesma forma que qualquer outra ligação. O exemplo seguinte define o objeto `response` de resposta utilizando uma ligação nomeada: 

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
+ **_[Apenas resposta]_ Chamando. `context.res.send(body?: any)`** Uma resposta HTTP é `body` criada com a entrada como o corpo de resposta. `context.done()`é implicitamente chamado.

+ **_[Apenas resposta]_ Chamando. `context.done()`** Um tipo especial de ligação HTTP devolve `context.done()` a resposta que é passada ao método. A seguinte ligação de `$return` saída HTTP define um parâmetro de saída:

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

## <a name="scaling-and-concurrency"></a>Escala e conmoeda

Por predefinição, as Funções Azure monitorizam automaticamente a carga na sua aplicação e criam instâncias adicionais para o Node.js, se necessário. Funções utilizam limiares incorporados (não configuráveis pelo utilizador) para diferentes tipos de gatilhos para decidir quando adicionar instâncias, tais como a idade das mensagens e o tamanho da fila para o QueueTrigger. Para mais informações, consulte [como funcionam os planos de Consumo e Premium.](functions-scale.md#how-the-consumption-and-premium-plans-work)

Este comportamento de escala é suficiente para muitas aplicações node.js. Para aplicações ligadas ao CPU, pode melhorar ainda mais o desempenho utilizando vários processos de trabalhadores linguísticos.

Por padrão, cada instância de anfitrião de Funções tem um único processo de trabalhador linguístico. Pode aumentar o número de processos de trabalhador por hospedeiro (até 10) utilizando a definição de aplicação [FUNCTIONS_WORKER_PROCESS_COUNT.](functions-app-settings.md#functions_worker_process_count) As Funções Azure tentam então distribuir uniformemente invocações de funções simultâneas por estes trabalhadores. 

O FUNCTIONS_WORKER_PROCESS_COUNT aplica-se a cada anfitrião que as Funções criam ao escalonar a sua aplicação para satisfazer a procura. 

## <a name="node-version"></a>Versão do nó

A tabela que se segue mostra as versões nónamos suportadas atuais para cada versão importante do tempo de funcionamento das Funções, por sistema operativo:

| Versão funções | Versão do nó (Windows) | Versão nó (Linux) |
|---|---| --- |
| 1.x | 6.11.2 (bloqueado pelo tempo de execução) | n/d |
| 2.x  | ~8<br/>~10 (recomendado)<br/>~12<sup>*</sup> | ~8 (recomendado)<br/>~10  |
| 3.x | ~10<br/>~12 (recomendado)  | ~10<br/>~12 (recomendado) |

<sup>*</sup>O nó ~12 é atualmente permitido na versão 2.x do tempo de funcionamento das Funções. No entanto, para um melhor desempenho, recomendamos a utilização da versão 3.x do tempo de funcionamento funções com o nó ~12. 

Pode ver a versão atual que o tempo de execução está `process.version` a ser utilizado verificando a definição de aplicação acima ou imprimindo a partir de qualquer função. Direcione a versão em Azure, definindo a definição `~10`de [aplicações](functions-how-to-use-azure-function-app-settings.md#settings) WEBSITE_NODE_DEFAULT_VERSION para uma versão LTS suportada, como .

## <a name="dependency-management"></a>Gestão de dependências
Para utilizar bibliotecas comunitárias no seu código JavaScript, como mostra o exemplo abaixo, precisa de garantir que todas as dependências estão instaladas na sua App de Funções em Azure.

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
> Deve definir `package.json` um ficheiro na raiz da sua App de Funções. A definição do ficheiro permite que todas as funções da aplicação partilhem os mesmos pacotes em cache, o que dá o melhor desempenho. Se surgir um conflito de versão, pode `package.json` resolvê-lo adicionando um ficheiro na pasta de uma função específica.  

Ao implementar as Aplicações de `package.json` Função a partir do `npm install` controlo de origem, qualquer ficheiro presente no seu repo, irá desencadear um na sua pasta durante a implementação. Mas ao ser implantado através do Portal ou CLI, terá de instalar manualmente as embalagens.

Existem duas formas de instalar pacotes na sua App de Funções: 

### <a name="deploying-with-dependencies"></a>Implantação com Dependências
1. Instale todos os pacotes necessários localmente, executando. `npm install`

2. Implemente o seu código `node_modules` e certifique-se de que a pasta está incluída na implementação. 


### <a name="using-kudu"></a>Usando Kudu
1. Aceda a `https://<function_app_name>.scm.azurewebsites.net`.

2. Clique na **consola de depuração** > **CMD**.

3. Vá `D:\home\site\wwwroot`para , e depois arraste o seu ficheiro package.json para a pasta **wwwroot** na metade superior da página.  
    Também pode fazer o upload de ficheiros para a sua aplicação de funções de outras formas. Para mais informações, consulte [como atualizar ficheiros de aplicações](functions-reference.md#fileupdate)de funções . 

4. Depois de o ficheiro package.json `npm install` ser carregado, execute o comando na consola de **execução remota kudu**.  
    Esta ação descarrega os pacotes indicados no ficheiro package.json e reinicia a aplicação de funções.

## <a name="environment-variables"></a>Variáveis de ambiente

Em Funções, [as definições](functions-app-settings.md)de aplicativos , tais como cordas de ligação ao serviço, são expostas como variáveis ambientais durante a execução. Pode aceder a `process.env`estas definições utilizando, como mostrado `context.log()` aqui na `AzureWebJobsStorage` segunda `WEBSITE_SITE_NAME` e terceira chamadas para onde registamos as variáveis e ambientais:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Ao executar localmente, as definições de aplicativos são lidas a partir do ficheiro do projeto [local.settings.json.](functions-run-local.md#local-settings-file)

## <a name="configure-function-entry-point"></a>Configurar o ponto de entrada da função

As `function.json` `scriptFile` propriedades `entryPoint` podem ser usadas para configurar a localização e o nome da sua função exportada. Estas propriedades podem ser importantes quando o seu JavaScript é transempilhado.

### <a name="using-scriptfile"></a>Utilizar `scriptFile`

Por predefinição, uma função `index.js`JavaScript é executada a partir de `function.json`um ficheiro que partilha o mesmo directório-mãe que o correspondente .

`scriptFile`pode ser usado para obter uma estrutura de pasta que se parece com o seguinte exemplo:

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

O `function.json` `myNodeFunction` for deve `scriptFile` incluir um imóvel que indique o ficheiro com a função exportada a executar.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Utilizar `entryPoint`

Em `scriptFile` (ou), `index.js`uma função `module.exports` deve ser exportada utilizando para ser encontrada e executada. Por predefinição, a função que executa quando desencadeada é `run`a única `index`exportação desse ficheiro, a exportação denominada , ou a exportação denominada .

Isto pode ser `entryPoint` configurado `function.json`utilizando, como no seguinte exemplo:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

Nas funções v2.x, que `this` suporta o parâmetro nas funções do utilizador, o código de função pode então ser como no seguinte exemplo:

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

## <a name="local-debugging"></a>Depuração Local

Quando iniciado `--inspect` com o parâmetro, um processo Node.js ouve um cliente depurado na porta especificada. Nas Funções Azure 2.x, pode especificar argumentos para passar para o processo Nó.js que `languageWorkers:node:arguments = <args>`executa o seu código adicionando a variável ambiental ou definição de aplicações . 

Para depurar `"languageWorkers:node:arguments": "--inspect=5858"` localmente, adicione no `Values` seu ficheiro [local.settings.json](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) e prenda um debugger à porta 5858.

Ao depurar-se utilizando `--inspect` o Código VS, `port` o parâmetro é adicionado automaticamente utilizando o valor no ficheiro launch.json do projeto.

Na versão 1.x, a definição `languageWorkers:node:arguments` não funcionará. A porta de depuração [`--nodeDebugPort`](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) pode ser selecionada com o parâmetro nas Ferramentas Nucleares das Funções Azure.

## <a name="typescript"></a>TypeScript

Quando direciona a versão 2.x do tempo de execução das funções, ambas as [funções Do Azure para o Código](functions-create-first-function-vs-code.md) do Estúdio Visual e as [Ferramentas Core funções do Azure](functions-run-local.md) permitem criar aplicações de função usando um modelo que suporta os projetos de aplicações de função TypeScript. O modelo `package.json` gera `tsconfig.json` e projeta ficheiros que facilitam a transpile, executar e publicar funções JavaScript a partir do código TypeScript com estas ferramentas.

Um ficheiro `.funcignore` gerado é utilizado para indicar quais os ficheiros excluídos quando um projeto é publicado no Azure.  

Os ficheiros TypeScript (.ts) são transempilhados em `dist` ficheiros JavaScript (.js) no diretório de saída. Os modelos typeScript utilizam `function.json` o [ `scriptFile` parâmetro](#using-scriptfile) para indicar a localização `dist` do ficheiro .js correspondente na pasta. A localização de saída é `outDir` definida pelo `tsconfig.json` modelo utilizando o parâmetro no ficheiro. Se alterar esta definição ou o nome da pasta, o tempo de execução não é capaz de encontrar o código a ser executado.

> [!NOTE]
> O suporte experimental para TypeScript existe a versão 1.x do tempo de execução das funções. A versão experimental transpõe ficheiros TypeScript para ficheiros JavaScript quando a função é invocada. Na versão 2.x, este suporte experimental foi substituído pelo método orientado pela ferramenta que faz a transpilação antes da inicialização do hospedeiro e durante o processo de implantação.

A forma como desenvolve e implanta localmente a partir de um projeto TypeScript depende da sua ferramenta de desenvolvimento.

### <a name="visual-studio-code"></a>Visual Studio Code

A extensão do Código do Estúdio Visual do [Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) permite desenvolver as suas funções utilizando o TypeScript. As Ferramentas Core são um requisito da extensão das Funções Azure.

Para criar uma aplicação de função `TypeScript` TypeScript no Código do Estúdio Visual, escolha como seu idioma quando criar uma aplicação de função.

Quando pressiona **F5** para executar a app localmente, a transpilação é feita antes de o anfitrião (func.exe) ser inicializado. 

Quando implementa a sua aplicação de função para o Azure utilizando o botão **Implementa...** botão, a extensão funções Do Azure gera primeiro uma construção pronta para a produção de ficheiros JavaScript a partir dos ficheiros de origem TypeScript.

### <a name="azure-functions-core-tools"></a>Ferramentas centrais de funções azure

Existem várias formas de um projeto TypeScript diferir de um projeto JavaScript ao utilizar as Ferramentas Core.

#### <a name="create-project"></a>Criar o projeto

Para criar um projeto de aplicação de função TypeScript utilizando ferramentas core, tem de especificar a opção de idioma TypeScript quando criar a sua aplicação de função. Pode fazê-lo de uma das seguintes formas:

- Executar `func init` o comando, selecionar `node` como a `typescript`sua pilha de idiomas e, em seguida, selecionar .

- Execute o comando `func init --worker-runtime typescript`.

#### <a name="run-local"></a>Correr localmente

Para executar o código da aplicação de funções `func host start`localmente utilizando ferramentas core, utilize os seguintes comandos em vez de: 

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

Antes de [`func azure functionapp publish`] utilizar o comando para ser implantado para o Azure, cria uma construção pronta para a produção de ficheiros JavaScript a partir dos ficheiros de origem TypeScript. 

Os seguintes comandos preparam e publicam o seu projeto TypeScript utilizando ferramentas core: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

Neste comando, `<APP_NAME>` substitua-o pelo nome da sua aplicação de funções.

## <a name="considerations-for-javascript-functions"></a>Considerações para funções JavaScript

Quando trabalhar com funções JavaScript, esteja atento às considerações nas seguintes secções.

### <a name="choose-single-vcpu-app-service-plans"></a>Escolha planos de serviço de aplicações single vCPU

Quando cria uma aplicação de função que utiliza o plano de Serviço de Aplicações, recomendamos que selecione um plano de vCPU único em vez de um plano com vários vCPUs. Hoje em dia, as funções executam as funções JavaScript de forma mais eficiente em VMs de vCPU simples, e a utilização de VMs maiores não produz as melhorias de desempenho esperadas. Quando necessário, pode escamas manualmente adicionando mais instâncias vCPU vCPU, ou pode ativar a escala automática. Para mais informações, consulte A contagem manual ou automática da [contagem da escala](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Início frio

Ao desenvolver funções Azure no modelo de hospedagem sem servidor, o arranque a frio é uma realidade. *O arranque* a frio refere-se ao facto de que quando a sua aplicação de funções começa pela primeira vez após um período de inatividade, demora mais tempo a iniciar-se. Para funções JavaScript com árvores de grande dependência em particular, o arranque a frio pode ser significativo. Para acelerar o processo de arranque a frio, [execute as suas funções como um ficheiro](run-functions-from-deployment-package.md) de embalagem quando possível. Muitos métodos de implementação usam a execução do modelo de pacote por padrão, mas se você está experimentando grandes arranques de frio e não está executando desta forma, esta mudança pode oferecer uma melhoria significativa.

### <a name="connection-limits"></a>Limites de ligação

Quando utilizar um cliente específico do serviço numa aplicação De Funções Azure, não crie um novo cliente com todas as funções de invocação. Em vez disso, crie um único cliente estático no âmbito global. Para mais informações, consulte [a gestão de ligações em Funções Azure](manage-connections.md).

### <a name="use-async-and-await"></a>Uso `async` e`await`

Ao escrever Funções Azure no JavaScript, `async` deve `await` escrever código utilizando as palavras-chave e palavras-chave. Escrever código `async` `await` utilizando e em `.then` vez `.catch` de chamadas ou com Promessas ajuda a evitar dois problemas comuns:
 - Lançando exceções não apanhadas que [invertam o processo Nó.js,](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly)potencialmente afetando a execução de outras funções.
 - Comportamento inesperado, como registos em falta do context.log, causadopor chamadas assíncronas que não são devidamente aguardadas.

No exemplo abaixo, o método `fs.readFile` assíncrono é invocado com uma função de chamada de primeira erro como segundo parâmetro. Este código causa ambas as questões acima mencionadas. Uma exceção que não é explicitamente apanhada no âmbito correto invadiu todo o processo (emissão #1). Ligar `context.done()` para fora do âmbito da função de chamada significa que a invocação da função pode terminar antes da leitura do ficheiro (emitir #2). Neste exemplo, `context.done()` a chamada demasiado cedo resulta `Data from file:`em falta de entradas de registo a partir de .

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

A `async` utilização das palavras-chave e `await` das palavras-chave ajuda a evitar ambos estes erros. Deve utilizar a função [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) de utilidade nonótipo.js para transformar funções de estilo de chamada em funções aguardadas.

No exemplo abaixo, quaisquer exceções não manipuladas lançadas durante a execução da função apenas falham a invocação individual que levantou uma exceção. A `await` palavra-chave significa `readFileAsync` que `readFile` os passos que se seguem só executam depois de concluídos. Com `async` `await`e. `context.done()`

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
+ [Funções Azure desencadeiam e encadernam](functions-triggers-bindings.md)

['Func azure functionapp publicar']: functions-run-local.md#project-file-deployment
