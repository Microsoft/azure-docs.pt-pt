---
title: Referência do desenvolvedor JavaScript para funções azure
description: Compreenda como desenvolver funções utilizando o JavaScript.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: reference
ms.date: 12/17/2019
ms.openlocfilehash: b0cd9541deac106525cfe80244d1867f513825f0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356068"
---
# <a name="azure-functions-javascript-developer-guide"></a>Guia de desenvolvedores de funções azure JavaScript

Este guia contém informações sobre as complexidades da escrita de Funções Azure com JavaScript.

Uma função JavaScript é uma `function` exportada que executa quando ativada (os[gatilhos são configurados em função.json](functions-triggers-bindings.md)). O primeiro argumento passado a todas as funções é um objeto `context`, que é usado para receber e enviar dados vinculativos, registar e comunicar com o tempo de execução.

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

Na raiz do projeto, há um ficheiro [host.json](functions-host-json.md) partilhado que pode ser usado para configurar a aplicação de função. Cada função tem uma pasta com o seu próprio ficheiro de código (.js) e ficheiro de configuração de ligação (função.json). O nome do diretório dos pais de `function.json`é sempre o nome da sua função.

As extensões de ligação exigidas na [versão 2.x](functions-versions.md) do tempo de execução das Funções são definidas no ficheiro `extensions.csproj`, com os ficheiros da biblioteca reais na pasta `bin`. Ao desenvolver-se localmente, deve [registar extensões vinculativas](./functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal Azure, este registo é feito para si.

## <a name="exporting-a-function"></a>Exportação de uma função

As funções JavaScript devem ser exportadas através [de`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (ou [`exports`). ](https://nodejs.org/api/modules.html#modules_exports) A sua função exportada deve ser uma função JavaScript que executa quando desencadeada.

Por predefinição, o tempo de funcionamento das Funções procura a sua função em `index.js`, onde `index.js` partilha o mesmo directório-mãe que o seu `function.json`correspondente . No caso de incumprimento, a sua função exportada deve ser a única exportação do seu ficheiro ou da exportação denominada `run` ou `index`. Para configurar a localização do ficheiro e o nome de exportação da sua função, leia sobre [a configuração do ponto de entrada da sua função](functions-reference-node.md#configure-function-entry-point) abaixo.

A sua função exportada é aprovada uma série de argumentos sobre a execução. O primeiro argumento é sempre um objeto `context`. Se a sua função for sincronizada (não devolve uma Promessa), deve passar o `context` objeto, uma vez que é necessário chamar `context.done` para uma utilização correta.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Exportação de uma função de assinos
Ao utilizar a declaração [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) ou [as promessas](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) simples do JavaScript na versão 2.x do tempo de funcionamento das Funções, não precisa de ligar explicitamente para o [`context.done`](#contextdone-method) chamada para sinalizar que a sua função está concluída. A sua função completa quando a função de asincronização exportada/Promessa termina. Para funções que visem o tempo de execução da versão 1.x, deve ainda ligar [`context.done`](#contextdone-method) quando o seu código terminar a execução.

O exemplo seguinte é uma função simples que regista que foi desencadeada e completa imediatamente a execução.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Ao exportar uma função de asincronização, também pode configurar uma ligação de saída para levar o valor `return`. Isto é recomendado se tiver apenas uma ligação de saída.

Para atribuir uma saída utilizando `return`, altere a propriedade `name` para `$return` em `function.json`.

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
A entrada é dividida em duas categorias em Funções Azure: uma é a entrada do gatilho e a outra é a entrada adicional. O gatilho e outras encadernações de entrada (encadernações de `direction === "in"`) podem ser lidas por uma função de três maneiras:
 - **_[Recomendado]_ À medida que os parâmetros passavam para a sua função.** São passados para a função na mesma ordem que são definidos em *função.json*. A propriedade `name` definida em *função.json* não precisa de corresponder ao nome do seu parâmetro, embora deva.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Como membros do [`context.bindings`](#contextbindings-property) objeto.** Cada membro é nomeado pela propriedade `name` definida em *função.json*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Como inputs usando o objeto [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) JavaScript.** Isto é essencialmente o mesmo que passar inputs como parâmetros, mas permite-lhe lidar com as inputs dinamicamente.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Resultado
As saídas (encadernações de `direction === "out"`) podem ser escritas por uma função de várias maneiras. Em todos os casos, a propriedade `name` da encadernação tal como definida na *função.json* corresponde ao nome do membro do objeto escrito na sua função. 

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

  Se estiver a utilizar uma função sincronizada, pode devolver este objeto usando [`context.done`](#contextdone-method) (ver exemplo).
- **_[Recomendado para uma saída única]_ Devolvendo um valor diretamente e utilizando o $return nome de ligação.** Isto funciona apenas para funções de retorno de sincronia/promessa. Consulte o exemplo na [exportação de uma função de asincronização](#exporting-an-async-function). 
- **Atribuir valores a `context.bindings`** Pode atribuir valores diretamente ao contexto.encadernações.

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

Para definir o tipo de dados para uma ligação de entrada, utilize a propriedade `dataType` na definição vinculativa. Por exemplo, para ler o conteúdo de um pedido HTTP em formato binário, utilize o tipo `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

As opções para `dataType` são: `binary`, `stream`e `string`.

## <a name="context-object"></a>objeto de contexto
O tempo de execução utiliza um `context` objeto para passar dados de e para a sua função e para permitir que se comunique com o tempo de funcionamento. O objeto de contexto pode ser usado para ler e definir dados a partir de encadernações, registos de escrita e utilização do `context.done` chamada quando a sua função exportada é sincronizada.

O `context` objeto é sempre o primeiro parâmetro para uma função. Deve ser incluído porque tem métodos importantes como `context.done` e `context.log`. Pode nomear o objeto o que quiser (por exemplo, `ctx` ou `c`).

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

Devolve um objeto nomeado que é usado para ler ou atribuir dados de ligação. Os dados de entrada e de disparo podem ser acedidos através da leitura de propriedades em `context.bindings`. Os dados de encadernação de saída podem ser atribuídos adicionando dados à `context.bindings`

Por exemplo, as seguintes definições de encadernação na sua função.json permitem-lhe aceder ao conteúdo de uma fila a partir de `context.bindings.myInput` e atribuir saídas a uma fila utilizando `context.bindings.myOutput`.

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

Pode optar por definir dados de ligação de saída utilizando o método `context.done` em vez do `context.binding` objeto (ver abaixo).

### <a name="contextbindingdata-property"></a>contexto.bindingPropriedadeData

```js
context.bindingData
```

Devolve um objeto nomeado que contém metadados de gatilho e dados de invocação de funções (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Para um exemplo de metadados de gatilho, consulte este [evento hubs exemplo](functions-bindings-event-hubs-trigger.md).

### <a name="contextdone-method"></a>método de contexto.feito

```js
context.done([err],[propertyBag])
```

Permite que o tempo de execução saiba que o seu código está completo. Quando a sua função utiliza a declaração [de`async function`,](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) não precisa de utilizar `context.done()`. O `context.done` chamada é implicitamente chamado. As funções de async estão disponíveis no nó 8 ou numa versão posterior, que requer a versão 2.x do tempo de execução das funções.

Se a sua função não for uma função de asincronização, **deve ligar** para `context.done` para informar o tempo de funcionamento de que a sua função está completa. A execução é a hora se faltar.

O método `context.done` permite-lhe passar de volta um erro definido pelo utilizador para o tempo de execução e um objeto JSON contendo dados de ligação de saída. As propriedades passaram para `context.done` sobrepor qualquer coisa definida no objeto `context.bindings`.

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

Permite-lhe escrever para os registos da função de streaming ao nível de rastreio predefinido. No `context.log`, existem métodos adicionais de exploração florestal que permitem escrever registos de funções noutros níveis de rastreio:


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

Em Funções, utiliza-se os métodos `context.log` para escrever a saída de vestígios para a consola. Nas funções v2.x, as saídas de rastreio utilizando `console.log` são capturadas ao nível da App de Funções. Isto significa que as saídas de `console.log` não estão ligadas a uma invocação de função específica e não são exibidas nos registos de uma função específica. No entanto, propagam-se à Aplicação Insights. Em Funções v1.x, não pode utilizar `console.log` para escrever para a consola.

Quando ligar `context.log()`, a sua mensagem é escrita para a consola ao nível de traço padrão, que é o nível de rastreio da _informação._ O seguinte código escreve para a consola ao nível do rastreio da informação:

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

Todos os métodos `context.log` suportam o mesmo formato de parâmetro que é suportado pelo método de [formato](https://nodejs.org/api/util.html#util_util_format_format)node.js . Considere o seguinte código, que escreve registos de funções utilizando o nível de traço predefinido:

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

As funções 1.x permitem definir o nível de traço limiar para a escrita na consola, o que facilita o controlo da forma como os vestígios são escritos para a consola a partir da sua função. Para definir o limiar para todos os vestígios escritos na consola, utilize a propriedade `tracing.consoleLevel` no ficheiro host.json. Esta definição aplica-se a todas as funções da sua aplicação de funções. O exemplo que se segue define o limiar do vestígio para permitir a exploração verbosa:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Os valores das **consolasNcorrespondem** aos nomes dos métodos `context.log`. Para desativar todos os vestígios de registo da consola, desative o **nível da consola** para _desligar_. Para mais informações, consulte [host.json reference](functions-host-json-v1.md).

## <a name="http-triggers-and-bindings"></a>HTTP gatilhos e encadernações

OS gatilhos HTTP e webhook e as encadernações de saída HTTP utilizam objetos de pedido e resposta para representar as mensagens HTTP.  

### <a name="request-object"></a>Objeto de pedido

O objeto `context.req` (pedido) tem as seguintes propriedades:

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

O objeto `context.res` (resposta) tem as seguintes propriedades:

| Propriedade  | Descrição                                               |
| --------- | --------------------------------------------------------- |
| _corpo_    | Um objeto que contém o corpo da resposta.         |
| _cabeçalhos_ | Um objeto que contém os cabeçalhos de resposta.             |
| _isRaw_   | Indica que a formatação é ignorada para a resposta.    |
| _estado_  | O código de estado HTTP da resposta.                     |

### <a name="accessing-the-request-and-response"></a>Acesso ao pedido e resposta 

Quando trabalha com os gatilhos HTTP, pode aceder aos objetos de pedido e resposta http de várias maneiras:

+ **De propriedades `req` e `res` no objeto `context`.** Desta forma, pode utilizar o padrão convencional para aceder aos dados HTTP do objeto de contexto, em vez de ter de usar o padrão de `context.bindings.name` completo. O exemplo que se segue mostra como aceder aos objetos `req` e `res` no `context`:

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Das ligações de entrada e saída nomeadas.** Desta forma, o gatilho HTTP e as encadernações funcionam da mesma forma que qualquer outra ligação. O exemplo seguinte define o objeto de resposta utilizando um chamado `response` vinculativo: 

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
+ **_[Apenas resposta]_ Chamando `context.res.send(body?: any)`.** Uma resposta HTTP é criada com a entrada `body` como o corpo de resposta. `context.done()` é implicitamente chamado.

+ **_[Apenas resposta]_ Chamando `context.done()`.** Um tipo especial de ligação HTTP devolve a resposta que é passada ao método `context.done()`. A seguinte ligação de saída HTTP define um parâmetro de saída `$return`:

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

<sup>*</sup> O nó ~12 é atualmente permitido na versão 2.x do tempo de funcionamento das Funções. No entanto, para um melhor desempenho, recomendamos a utilização da versão 3.x do tempo de funcionamento funções com o nó ~12. 

Pode ver a versão atual que o tempo de execução está a ser utilizado verificando a definição de aplicação acima ou imprimindo `process.version` de qualquer função. Direcione a versão em Azure, definindo a definição de [aplicações](functions-how-to-use-azure-function-app-settings.md#settings) WEBSITE_NODE_DEFAULT_VERSION para uma versão LTS suportada, como `~10`.

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
> Deve definir um ficheiro `package.json` na raiz da sua App de Funções. A definição do ficheiro permite que todas as funções da aplicação partilhem os mesmos pacotes em cache, o que dá o melhor desempenho. Se surgir um conflito de versão, pode resolvê-lo adicionando um ficheiro `package.json` na pasta de uma função específica.  

Ao implementar as Aplicações de Função a partir do controlo de origem, qualquer ficheiro `package.json` presente no seu repo, irá desencadear uma `npm install` na sua pasta durante a implementação. Mas ao ser implantado através do Portal ou CLI, terá de instalar manualmente as embalagens.

Existem duas formas de instalar pacotes na sua App de Funções: 

### <a name="deploying-with-dependencies"></a>Implantação com Dependências
1. Instale todos os pacotes necessários localmente executando `npm install`.

2. Implemente o seu código e certifique-se de que a pasta `node_modules` está incluída na implementação. 


### <a name="using-kudu"></a>Usando Kudu
1. Aceda a `https://<function_app_name>.scm.azurewebsites.net`.

2. Clique na **consola de depuração** > **CMD**.

3. Vá para `D:\home\site\wwwroot`e, em seguida, arraste o ficheiro package.json para a pasta **wwwroot** na metade superior da página.  
    Também pode fazer o upload de ficheiros para a sua aplicação de funções de outras formas. Para mais informações, consulte [como atualizar ficheiros de aplicações](functions-reference.md#fileupdate)de funções . 

4. Depois de o ficheiro package.json ser carregado, execute o comando `npm install` na consola de **execução remota kudu**.  
    Esta ação descarrega os pacotes indicados no ficheiro package.json e reinicia a aplicação de funções.

## <a name="environment-variables"></a>Variáveis de ambiente

Em Funções, [as definições](functions-app-settings.md)de aplicativos , tais como cordas de ligação ao serviço, são expostas como variáveis ambientais durante a execução. Pode aceder a estas definições utilizando `process.env`, como mostra aqui na segunda e terceira chamadas para `context.log()` onde registamos as variáveis ambiente `AzureWebJobsStorage` e `WEBSITE_SITE_NAME`:

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

As propriedades `function.json` `scriptFile` e `entryPoint` podem ser usadas para configurar a localização e o nome da sua função exportada. Estas propriedades podem ser importantes quando o seu JavaScript é transempilhado.

### <a name="using-scriptfile"></a>Usando `scriptFile`

Por predefinição, uma função JavaScript é executada a partir de `index.js`, um ficheiro que partilha o mesmo directório-mãe que o seu `function.json`correspondente .

`scriptFile` pode ser usado para obter uma estrutura de pasta que se parece com o seguinte exemplo:

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

O `function.json` para `myNodeFunction` deve incluir uma propriedade `scriptFile` que indique o ficheiro com a função exportada a executar.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Usando `entryPoint`

Em `scriptFile` (ou `index.js`), deve ser exportada uma função utilizando `module.exports` para ser encontrada e executada. Por predefinição, a função que executa quando desencadeada é a única exportação desse ficheiro, a exportação denominada `run`, ou a exportação denominada `index`.

Isto pode ser configurado utilizando `entryPoint` em `function.json`, como no seguinte exemplo:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

Nas funções v2.x, que suporta o parâmetro `this` nas funções do utilizador, o código de função pode então ser como no seguinte exemplo:

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

Quando iniciado com o parâmetro `--inspect`, um processo Node.js ouve um cliente depurado na porta especificada. Nas Funções Azure 2.x, pode especificar argumentos para passar para o processo Nó.js que executa o seu código adicionando a variável ambiental ou definição de aplicações `languageWorkers:node:arguments = <args>`. 

Para depurar localmente, adicione `"languageWorkers:node:arguments": "--inspect=5858"` sob `Values` no ficheiro [local.settings.json](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) e prenda um desbugger à porta 5858.

Ao depurar-se utilizando o Código VS, o parâmetro `--inspect` é adicionado automaticamente utilizando o valor `port` no ficheiro launch.json do projeto.

Na versão 1.x, a definição `languageWorkers:node:arguments` não funcionará. A porta de depuração pode ser selecionada com o parâmetro [`--nodeDebugPort`](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) em Ferramentas Nucleares de Funções Azure.

## <a name="typescript"></a>TypeScript

Quando direciona a versão 2.x do tempo de execução das funções, ambas as [funções Do Azure para o Código](functions-create-first-function-vs-code.md) do Estúdio Visual e as [Ferramentas Core funções do Azure](functions-run-local.md) permitem criar aplicações de função usando um modelo que suporta os projetos de aplicações de função TypeScript. O modelo gera `package.json` e `tsconfig.json` ficheiros de projeto que facilitam a transpile, executar e publicar funções JavaScript a partir do código TypeScript com estas ferramentas.

Um ficheiro `.funcignore` gerado é usado para indicar quais os ficheiros excluídos quando um projeto é publicado no Azure.  

Os ficheiros TypeScript (.ts) são transempilhados em ficheiros JavaScript (.js) no diretório de saída `dist`. Os modelos typeScript utilizam o [parâmetro`scriptFile`](#using-scriptfile) em `function.json` para indicar a localização do ficheiro .js correspondente na pasta `dist`. A localização de saída é definida pelo modelo utilizando `outDir` parâmetro no ficheiro `tsconfig.json`. Se alterar esta definição ou o nome da pasta, o tempo de execução não é capaz de encontrar o código a ser executado.

> [!NOTE]
> O suporte experimental para TypeScript existe a versão 1.x do tempo de execução das funções. A versão experimental transpõe ficheiros TypeScript para ficheiros JavaScript quando a função é invocada. Na versão 2.x, este suporte experimental foi substituído pelo método orientado pela ferramenta que faz a transpilação antes da inicialização do hospedeiro e durante o processo de implantação.

A forma como desenvolve e implanta localmente a partir de um projeto TypeScript depende da sua ferramenta de desenvolvimento.

### <a name="visual-studio-code"></a>Visual Studio Code

A extensão do Código do Estúdio Visual do [Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) permite desenvolver as suas funções utilizando o TypeScript. As Ferramentas Core são um requisito da extensão das Funções Azure.

Para criar uma aplicação de função TypeScript no Código do Estúdio Visual, escolha `TypeScript` como seu idioma quando criar uma aplicação de função.

Quando pressiona **F5** para executar a app localmente, a transpilação é feita antes de o anfitrião (func.exe) ser inicializado. 

Quando implementa a sua aplicação de função para o Azure utilizando o botão **Implementa...** botão, a extensão funções Do Azure gera primeiro uma construção pronta para a produção de ficheiros JavaScript a partir dos ficheiros de origem TypeScript.

### <a name="azure-functions-core-tools"></a>Ferramentas centrais de funções azure

Existem várias formas de um projeto TypeScript diferir de um projeto JavaScript ao utilizar as Ferramentas Core.

#### <a name="create-project"></a>Criar o projeto

Para criar um projeto de aplicação de função TypeScript utilizando ferramentas core, tem de especificar a opção de idioma TypeScript quando criar a sua aplicação de função. Pode fazê-lo de uma das seguintes formas:

- Execute o comando `func init`, selecione `node` como pilha de idiomas e, em seguida, selecione `typescript`.

- Execute o comando `func init --worker-runtime typescript`.

#### <a name="run-local"></a>Correr localmente

Para executar o código da aplicação de funções localmente utilizando ferramentas core, utilize os seguintes comandos em vez de `func host start`: 

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

Antes de utilizar o comando [`func azure functionapp publish`] para ser implantado para o Azure, cria uma construção pronta para a produção de ficheiros JavaScript a partir dos ficheiros de origem TypeScript. 

Os seguintes comandos preparam e publicam o seu projeto TypeScript utilizando ferramentas core: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

Neste comando, substitua `<APP_NAME>` pelo nome da sua aplicação de funções.

## <a name="considerations-for-javascript-functions"></a>Considerações para funções JavaScript

Quando trabalhar com funções JavaScript, esteja atento às considerações nas seguintes secções.

### <a name="choose-single-vcpu-app-service-plans"></a>Escolha planos de serviço de aplicações single vCPU

Quando cria uma aplicação de função que utiliza o plano de Serviço de Aplicações, recomendamos que selecione um plano de vCPU único em vez de um plano com vários vCPUs. Hoje em dia, as funções executam as funções JavaScript de forma mais eficiente em VMs de vCPU simples, e a utilização de VMs maiores não produz as melhorias de desempenho esperadas. Quando necessário, pode escamas manualmente adicionando mais instâncias vCPU vCPU, ou pode ativar a escala automática. Para mais informações, consulte A contagem manual ou automática da [contagem da escala](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Início frio

Ao desenvolver funções Azure no modelo de hospedagem sem servidor, o arranque a frio é uma realidade. *O arranque* a frio refere-se ao facto de que quando a sua aplicação de funções começa pela primeira vez após um período de inatividade, demora mais tempo a iniciar-se. Para funções JavaScript com árvores de grande dependência em particular, o arranque a frio pode ser significativo. Para acelerar o processo de arranque a frio, [execute as suas funções como um ficheiro](run-functions-from-deployment-package.md) de embalagem quando possível. Muitos métodos de implementação usam a execução do modelo de pacote por padrão, mas se você está experimentando grandes arranques de frio e não está executando desta forma, esta mudança pode oferecer uma melhoria significativa.

### <a name="connection-limits"></a>Limites de ligação

Quando utilizar um cliente específico do serviço numa aplicação De Funções Azure, não crie um novo cliente com todas as funções de invocação. Em vez disso, crie um único cliente estático no âmbito global. Para mais informações, consulte [a gestão de ligações em Funções Azure](manage-connections.md).

### <a name="use-async-and-await"></a>Use `async` e `await`

Ao escrever Funções Azure no JavaScript, deve escrever código utilizando as palavras-chave `async` e `await`. Escrever código utilizando `async` e `await` em vez de chamadas ou `.then` e `.catch` com Promessas ajuda a evitar dois problemas comuns:
 - Lançando exceções não apanhadas que [invertam o processo Nó.js,](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly)potencialmente afetando a execução de outras funções.
 - Comportamento inesperado, como registos em falta do context.log, causadopor chamadas assíncronas que não são devidamente aguardadas.

No exemplo abaixo, o método assíncrono `fs.readFile` é invocado com uma função de chamada de primeira erro como segundo parâmetro. Este código causa ambas as questões acima mencionadas. Uma exceção que não é explicitamente apanhada no âmbito correto invadiu todo o processo (emissão #1). Chamar `context.done()` fora do âmbito da função de chamada significa que a invocação da função pode terminar antes da leitura do ficheiro (emitir #2). Neste exemplo, chamar `context.done()` resultados demasiado precoces em falta de entradas de registo a partir de `Data from file:`.

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

A utilização das palavras-chave `async` e `await` ajuda a evitar ambos estes erros. Deve utilizar a [função](https://nodejs.org/api/util.html#util_util_promisify_original) de utilidade nonó`util.promisify`para transformar funções de estilo de chamada em funções aguardadas.

No exemplo abaixo, quaisquer exceções não manipuladas lançadas durante a execução da função apenas falham a invocação individual que levantou uma exceção. A palavra-chave `await` significa que os passos que se seguem `readFileAsync` só executam após `readFile` estar concluído. Com `async` e `await`, também não precisa de ligar para o `context.done()` chamada.

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

## <a name="next-steps"></a>Passos Seguintes

Para mais informações, consulte os seguintes recursos:

+ [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
+ [Referência para programadores das Funções do Azure](functions-reference.md)
+ [Funções Azure desencadeiam e encadernam](functions-triggers-bindings.md)

['Func azure functionapp publicar']: functions-run-local.md#project-file-deployment
