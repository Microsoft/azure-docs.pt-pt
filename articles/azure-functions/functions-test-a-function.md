---
title: Testar as Funções do Azure
description: Criar testes automatizados para uma função C# em Estúdio Visual e Função JavaScript em Código VS
author: craigshoemaker
ms.topic: conceptual
ms.custom: devx-track-csharp, devx-track-js
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: 8ff70c14310dd81a051ac27c1d6d59bb3d1deb7b
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677611"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Estratégias para testar o seu código nas Funções do Azure

Este artigo demonstra como criar testes automatizados para Funções Azure.

É recomendável testar todo o código, no entanto poderá obter os melhores resultados, embrulhando a lógica de uma Função e criando testes fora da Função. A abstração da lógica de distância limita as linhas de código de uma Função e permite que a Função seja a única responsável por chamar outras classes ou módulos. Este artigo, no entanto, demonstra como criar testes automatizados contra um HTTP e funções desencadeadas por temporizador.

O conteúdo que se segue é dividido em duas secções diferentes destinadas a direcionar diferentes línguas e ambientes. Pode aprender a fazer testes em:

- [C# em Estúdio Visual com xUnit](#c-in-visual-studio)
- [JavaScript em Código VS com Brincadeira](#javascript-in-vs-code)

O repositório de amostras está disponível no [GitHub.](https://github.com/Azure-Samples/azure-functions-tests)

## <a name="c-in-visual-studio"></a>C# no Estúdio Visual

O exemplo a seguir descreve como criar uma aplicação de função C# no Estúdio Visual e executar e testar com [xUnit](https://xunit.github.io).

![Testar funções Azure com C# em Estúdio Visual](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Configuração

Para configurar o seu ambiente, crie uma aplicação de função e teste. Os seguintes passos ajudam-no a criar as aplicações e funções necessárias para suportar os testes:

1. [Criar uma nova aplicação funções](./functions-create-first-azure-function.md) e nomeá-la **Funções**
2. [Crie uma função HTTP a partir do modelo](./functions-create-first-azure-function.md) e nomeie-o **MyHttpTrigger** .
3. [Crie uma função temporizador a partir do modelo](./functions-create-scheduled-function.md) e nomeie-o **MyTimerTrigger** .
4. [Crie uma aplicação de teste xUnit](https://xunit.github.io/docs/getting-started-dotnet-core) na solução e nomeie-a **Funções.Testes** .
5. Utilize o NuGet para adicionar uma referência da aplicação de teste à [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Referenciar a aplicação *Funções*](/visualstudio/ide/managing-references-in-a-project?view=vs-2017) a partir da aplicação *Funções.Testes.*

### <a name="create-test-classes"></a>Criar aulas de teste

Agora que os projetos são criados, pode criar as classes usadas para executar os testes automatizados.

Cada função requer um exemplo de [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) para lidar com o registo de mensagens. Alguns testes ou não registam mensagens ou não têm qualquer preocupação com a forma como o registo é implementado. Outros testes precisam de avaliar as mensagens registadas para determinar se um teste está a passar.

Irá criar uma nova classe com o nome `ListLogger` de uma lista interna de mensagens para avaliar durante um teste. Para implementar a `ILogger` interface necessária, a classe precisa de um âmbito. A classe seguinte ridiculariza uma margem para que os casos de teste passem para a `ListLogger` classe.

Criar uma nova classe em *Funções.Testes* projeto nomeado **NullScope.cs** e introduza o seguinte código:

```csharp
using System;

namespace Functions.Tests
{
    public class NullScope : IDisposable
    {
        public static NullScope Instance { get; } = new NullScope();

        private NullScope() { }

        public void Dispose() { }
    }
}
```

Em seguida, crie uma nova classe em *Funções.Testes* projeto nomeado **ListLogger.cs** e introduza o seguinte código:

```csharp
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Text;

namespace Functions.Tests
{
    public class ListLogger : ILogger
    {
        public IList<string> Logs;

        public IDisposable BeginScope<TState>(TState state) => NullScope.Instance;

        public bool IsEnabled(LogLevel logLevel) => false;

        public ListLogger()
        {
            this.Logs = new List<string>();
        }

        public void Log<TState>(LogLevel logLevel,
                                EventId eventId,
                                TState state,
                                Exception exception,
                                Func<TState, Exception, string> formatter)
        {
            string message = formatter(state, exception);
            this.Logs.Add(message);
        }
    }
}
```

A `ListLogger` classe implementa os seguintes membros conforme contratado pela `ILogger` interface:

- **StartScope** : Os âmbitos adicionam contexto à sua sessão de registo. Neste caso, o teste apenas aponta para a instância estática da `NullScope` classe para permitir o funcionamento do teste.

- **IsEnabled:** É fornecido um valor `false` predefinido.

- **Log** : Este método utiliza a função fornecida `formatter` para formatar a mensagem e, em seguida, adicionar o texto resultante à `Logs` coleção.

A `Logs` coleção é um exemplo de e é `List<string>` inicializada no construtor.

Em seguida, crie um novo ficheiro em *Funções.Testes* projeto denominado **LoggerTypes.cs** e introduza o seguinte código:

```csharp
namespace Functions.Tests
{
    public enum LoggerTypes
    {
        Null,
        List
    }
}
```

Esta enumeração especifica o tipo de madeirão utilizado pelos testes.

Agora crie uma nova classe em *Funções.Testes* projeto chamado **TestFactory.cs** e introduza o seguinte código:

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Http.Internal;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions;
using Microsoft.Extensions.Primitives;
using System.Collections.Generic;

namespace Functions.Tests
{
    public class TestFactory
    {
        public static IEnumerable<object[]> Data()
        {
            return new List<object[]>
            {
                new object[] { "name", "Bill" },
                new object[] { "name", "Paul" },
                new object[] { "name", "Steve" }

            };
        }

        private static Dictionary<string, StringValues> CreateDictionary(string key, string value)
        {
            var qs = new Dictionary<string, StringValues>
            {
                { key, value }
            };
            return qs;
        }

        public static HttpRequest CreateHttpRequest(string queryStringKey, string queryStringValue)
        {
            var context = new DefaultHttpContext();
            var request = context.Request;
            request.Query = new QueryCollection(CreateDictionary(queryStringKey, queryStringValue));
            return request;
        }

        public static ILogger CreateLogger(LoggerTypes type = LoggerTypes.Null)
        {
            ILogger logger;

            if (type == LoggerTypes.List)
            {
                logger = new ListLogger();
            }
            else
            {
                logger = NullLoggerFactory.Instance.CreateLogger("Null Logger");
            }

            return logger;
        }
    }
}
```

A `TestFactory` classe implementa os seguintes membros:

- **Dados** : Esta propriedade devolve uma recolha [IEnumerable](/dotnet/api/system.collections.ienumerable) de dados de amostra. Os pares de valor-chave representam valores que são passados para uma cadeia de consulta.

- **CreateDictionary** : Este método aceita um par chave/valor como argumentos e devolve um novo `Dictionary` usado para criar para representar `QueryCollection` valores de cadeia de consulta.

- **CreateHttpRequest** : Este método cria um pedido HTTP inicializado com os parâmetros de cadeia de consulta.

- **CreateLogger** : Com base no tipo madeirão, este método devolve uma classe de madeireiros utilizada para testes. O `ListLogger` registo de mensagens registadas disponível para avaliação em testes.

Finalmente, crie uma nova classe em *Funções.Testes* projeto nomeado **FunctionsTests.cs** e introduza o seguinte código:

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Xunit;

namespace Functions.Tests
{
    public class FunctionsTests
    {
        private readonly ILogger logger = TestFactory.CreateLogger();

        [Fact]
        public async void Http_trigger_should_return_known_string()
        {
            var request = TestFactory.CreateHttpRequest("name", "Bill");
            var response = (OkObjectResult)await MyHttpTrigger.Run(request, logger);
            Assert.Equal("Hello, Bill. This HTTP triggered function executed successfully.", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await MyHttpTrigger.Run(request, logger);
            Assert.Equal($"Hello, {queryStringValue}. This HTTP triggered function executed successfully.", response.Value);
        }

        [Fact]
        public void Timer_should_log_message()
        {
            var logger = (ListLogger)TestFactory.CreateLogger(LoggerTypes.List);
            MyTimerTrigger.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```

Os membros implementados nesta classe são:

- **Http_trigger_should_return_known_string** : Este teste cria um pedido com os valores de cadeia de consulta de `name=Bill` uma função HTTP e verifica se a resposta esperada é devolvida.

- **Http_trigger_should_return_string_from_member_data:** Este teste utiliza atributos xUnit para fornecer dados de amostra à função HTTP.

- **Timer_should_log_message** : Este teste cria uma instância `ListLogger` de e passa-a para funções de temporizador. Uma vez executada a função, o registo é verificado para garantir que a mensagem esperada está presente.

Se pretender aceder às definições da aplicação nos seus testes, pode utilizar [System.Environment.GetEnvironmentVariable](./functions-dotnet-class-library.md#environment-variables).

### <a name="run-tests"></a>Testes de execução

Para realizar os testes, navegue no Explorador de **Teste** e clique em **Executar tudo** .

![Testar funções Azure com C# em Estúdio Visual](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Testes de depurgação

Para depurar os testes, desfera um ponto de rutura num teste, navegue no **Explorador de Teste** e clique em Executar > Última Corrida de **Debug** .

## <a name="javascript-in-vs-code"></a>JavaScript em Código VS

O exemplo a seguir descreve como criar uma aplicação JavaScript Function em VS Code e executar e testar com [o Jest.](https://jestjs.io) Este procedimento utiliza a [extensão vs Code Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para criar funções Azure.

![Testar funções Azure com JavaScript em Código VS](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Configuração

Para configurar o seu ambiente, inicialize uma nova aplicação Node.js numa pasta vazia executando `npm init` .

```bash
npm init -y
```

Em seguida, instale o Jest executando o seguinte comando:

```bash
npm i jest
```

Agora atualize _package.js_ para substituir o comando de teste existente pelo seguinte comando:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Criar módulos de teste

Com o projeto inicializado, pode criar os módulos utilizados para executar os testes automatizados. Comece por criar uma nova pasta chamada *teste para* manter os módulos de suporte.

Na pasta de *teste* adicione um novo ficheiro, nomeie-o **defaultContext.js** , e adicione o seguinte código:

```javascript
module.exports = {
    log: jest.fn()
};
```

Este módulo ridiculariza a função *de registo* para representar o contexto de execução predefinido.

Em seguida, adicione um novo ficheiro, nomeie-o **defaultTimer.js** , e adicione o seguinte código:

```javascript
module.exports = {
    IsPastDue: false
};
```

Este módulo implementa a `IsPastDue` propriedade para ficar é como uma instância de temporizador falso. Configurações de temporizador como expressões NCRONTAB não são necessárias aqui, uma vez que o arnês de teste está simplesmente chamando a função diretamente para testar o resultado.

Em seguida, utilize a extensão vs Code Functions para [criar uma nova função JAVAScript HTTP](/azure/developer/javascript/tutorial-vscode-serverless-node-01) e nomeie-a *HttpTrigger* . Uma vez criada a função, adicione um novo ficheiro na mesma pasta denominada **index.test.js** , e adicione o seguinte código:

```javascript
const httpFunction = require('./index');
const context = require('../testing/defaultContext')

test('Http trigger should return known text', async () => {

    const request = {
        query: { name: 'Bill' }
    };

    await httpFunction(context, request);

    expect(context.log.mock.calls.length).toBe(1);
    expect(context.res.body).toEqual('Hello Bill');
});
```

A função HTTP do modelo devolve uma série de "Olá" concatenated com o nome fornecido na cadeia de consulta. Este teste cria uma instância falsa de um pedido e passa-o para a função HTTP. O teste verifica se o método do *registo* é chamado uma vez e o texto devolvido é igual a "Olá Bill".

Em seguida, utilize a extensão vs Code Functions para criar uma nova Função de Temporizador JavaScript e nomeie-o *TimerTrigger* . Uma vez criada a função, adicione um novo ficheiro na mesma pasta denominada **index.test.js** , e adicione o seguinte código:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```

A função temporizador do modelo regista uma mensagem no final do corpo da função. Este teste garante que a função *de registo* é chamada uma vez.

### <a name="run-tests"></a>Testes de execução

Para realizar os testes, prima **CTRL + ~** para abrir a janela de comando, e `npm test` executar:

```bash
npm test
```

![Testar funções Azure com JavaScript em Código VS](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Testes de depurgação

Para depurar os seus testes, adicione a seguinte configuração à sua *launch.jsno* ficheiro:

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "disableOptimisticBPs": true,
  "program": "${workspaceRoot}/node_modules/jest/bin/jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

Em seguida, desaponte um ponto de rutura no seu teste e prima **F5** .

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a escrever testes automatizados para as suas funções, continue com estes recursos:

- [Executar manualmente uma função não acionada por HTTP](./functions-manually-run-non-http.md)
- [Tratamento de erros de funções Azure](./functions-bindings-error-pages.md)
- [Grelha de evento de função Azure desencadeia depuração local](./functions-debug-event-grid-trigger-local.md)
