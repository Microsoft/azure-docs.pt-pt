---
title: Testar as Funções do Azure
description: Criar testes automatizados para C# uma função no Visual Studio e na função JavaScript no vs Code
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, funções, processamento de eventos, WebHooks, computação dinâmica, arquitetura sem servidor, testes
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: 9155df315a5afb9a0fa7722c955333a47a73085a
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596843"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Estratégias para testar seu código no Azure Functions

Este artigo demonstra como criar testes automatizados para Azure Functions. 

O teste de todo o código é recomendado, no entanto, você pode obter os melhores resultados ao resumir a lógica de uma função e criar testes fora da função. A abstração da lógica afasta os limites das linhas de código de uma função e permite que a função seja exclusivamente responsável pela chamada de outras classes ou módulos. Este artigo, no entanto, demonstra como criar testes automatizados em uma função HTTP e disparada por temporizador.

O conteúdo a seguir é dividido em duas seções diferentes, destinadas a direcionar diferentes idiomas e ambientes. Você pode aprender a criar testes no:

- [C#no Visual Studio com xUnit](#c-in-visual-studio)
- [JavaScript em VS Code com Jest](#javascript-in-vs-code)

O repositório de exemplo está disponível no [GitHub](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>C#no Visual Studio
O exemplo a seguir descreve como criar um C# aplicativo de funções no Visual Studio e executar e testar com o [xUnit](https://xunit.github.io).

![Testando Azure Functions C# com o no Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Configurar

Para configurar seu ambiente, crie uma função e teste o aplicativo. As etapas a seguir ajudam a criar os aplicativos e funções necessários para dar suporte aos testes:

1. [Criar um novo aplicativo do Functions](./functions-create-first-azure-function.md) e nomeá-lo como *funções*
2. [Crie uma função http a partir do modelo](./functions-create-first-azure-function.md) e nomeie-a *HttpTrigger*.
3. [Crie uma função de temporizador a partir do modelo](./functions-create-scheduled-function.md) e nomeie-a *TimerTrigger*.
4. [Crie um aplicativo de teste do xUnit](https://xunit.github.io/docs/getting-started-dotnet-core) no Visual Studio clicando em **arquivo > novo > projeto C# > Visual > .NET Core > projeto de teste do xUnit** e nomeie-o como *functions. Test*. 
5. Use o NuGet para adicionar uma referência do aplicativo de teste [Microsoft. AspNetCore. Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Referencie o aplicativo *Functions* ](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017) do *functions. Test* app.

### <a name="create-test-classes"></a>Criar classes de teste

Agora que os aplicativos são criados, você pode criar as classes usadas para executar os testes automatizados.

Cada função usa uma instância de [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) para manipular o log de mensagens. Alguns testes não registram mensagens ou não se preocupam com a forma como o log é implementado. Outros testes precisam avaliar as mensagens registradas para determinar se um teste está sendo aprovado.

A classe `ListLogger` destina-se a implementar a interface `ILogger` e manter a lista interna de mensagens para avaliação durante um teste.

**Clique com o botão direito do mouse** no aplicativo *functions. Test* e selecione **Adicionar > classe**, nomeie-o **NullScope.cs** e insira o código a seguir:

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

Em seguida, clique com o **botão direito do mouse** no aplicativo *functions. Test* e selecione **Adicionar > classe**, nomeie-o **ListLogger.cs** e insira o código a seguir:

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

A classe `ListLogger` implementa os seguintes membros como contratados pela interface `ILogger`:

- **BeginScope**: os escopos adicionam contexto ao seu registro em log. Nesse caso, o teste apenas aponta para a instância estática na classe `NullScope` para permitir que o teste funcione.

- **IsEnabled**: um valor padrão de `false` é fornecido.

- **Log**: esse método usa a função de `formatter` fornecida para formatar a mensagem e, em seguida, adiciona o texto resultante à coleção de `Logs`.

A coleção de `Logs` é uma instância de `List<string>` e é inicializada no construtor.

Em seguida, clique com o **botão direito do mouse** no aplicativo *functions. Test* e selecione **Adicionar > classe**, nomeie-o **LoggerTypes.cs** e insira o código a seguir:

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
Essa enumeração Especifica o tipo de agente de log usado pelos testes. 

Em seguida, clique com o **botão direito do mouse** no aplicativo *functions. Test* e selecione **Adicionar > classe**, nomeie-o **TestFactory.cs** e insira o código a seguir:

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

        public static DefaultHttpRequest CreateHttpRequest(string queryStringKey, string queryStringValue)
        {
            var request = new DefaultHttpRequest(new DefaultHttpContext())
            {
                Query = new QueryCollection(CreateDictionary(queryStringKey, queryStringValue))
            };
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
A classe `TestFactory` implementa os seguintes membros:

- **Dados**: essa propriedade retorna uma coleção [IEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) de dados de exemplo. Os pares chave-valor representam valores que são passados para uma cadeia de caracteres de consulta.

- **CreateDictionary**: esse método aceita um par de chave/valor como argumentos e retorna um novo `Dictionary` usado para criar `QueryCollection` para representar valores de cadeia de caracteres de consulta.

- **Createhttprequest**: esse método cria uma solicitação HTTP inicializada com os parâmetros de cadeia de caracteres de consulta fornecidos.

- **Createlogger**: com base no tipo de agente, esse método retorna uma classe de agente usada para teste. O `ListLogger` controla as mensagens registradas disponíveis para avaliação em testes.

Em seguida, clique com o **botão direito do mouse** no aplicativo *functions. Test* e selecione **Adicionar > classe**, nomeie-o **FunctionsTests.cs** e insira o código a seguir:

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
            var response = (OkObjectResult)await HttpFunction.Run(request, logger);
            Assert.Equal("Hello, Bill", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await HttpFunction.Run(request, logger);
            Assert.Equal($"Hello, {queryStringValue}", response.Value);
        }

        [Fact]
        public void Timer_should_log_message()
        {
            var logger = (ListLogger)TestFactory.CreateLogger(LoggerTypes.List);
            TimerTrigger.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```
Os membros implementados nessa classe são:

- **Http_trigger_should_return_known_string**: esse teste cria uma solicitação com os valores de cadeia de caracteres de consulta de `name=Bill` para uma função http e verifica se a resposta esperada é retornada.

- **Http_trigger_should_return_string_from_member_data**: esse teste usa atributos xUnit para fornecer dados de exemplo para a função http.

- **Timer_should_log_message**: esse teste cria uma instância de `ListLogger` e passa-a para uma função de temporizador. Depois que a função for executada, o log será verificado para garantir que a mensagem esperada esteja presente.

Se você quiser acessar as configurações do aplicativo em seus testes, você pode usar [System. Environment. GetEnvironmentVariable](./functions-dotnet-class-library.md#environment-variables).

### <a name="run-tests"></a>Executar testes

Para executar os testes, navegue até o **Gerenciador de testes** e clique em **executar tudo**.

![Testando Azure Functions C# com o no Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Depurar testes

Para depurar os testes, defina um ponto de interrupção em um teste, navegue até o **Gerenciador de testes** e clique em **Executar > última execução de depuração**.

## <a name="javascript-in-vs-code"></a>JavaScript em VS Code

O exemplo a seguir descreve como criar um aplicativo de funções JavaScript no VS Code e executar e testar com o [Jest](https://jestjs.io). Este procedimento usa a [extensão de funções vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para criar Azure functions.

![Testando Azure Functions com JavaScript em VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Configurar

Para configurar seu ambiente, inicialize um novo aplicativo node. js em uma pasta vazia executando `npm init`.

```bash
npm init -y
```
Em seguida, instale o Jest executando o seguinte comando:

```bash
npm i jest
```
Agora, atualize _Package. JSON_ para substituir o comando de teste existente pelo seguinte comando:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Criar módulos de teste
Com o projeto inicializado, você pode criar os módulos usados para executar os testes automatizados. Comece criando uma nova pasta chamada *Testing* para manter os módulos de suporte.

Na pasta de *teste* , adicione um novo arquivo, nomeie-o como **DefaultContext. js**e adicione o seguinte código:

```javascript
module.exports = {
    log: jest.fn()
};
```
Esse módulo simula a função de *log* para representar o contexto de execução padrão.

Em seguida, adicione um novo arquivo, nomeie-o como **Defaulttimer. js**e adicione o seguinte código:

```javascript
module.exports = {
    IsPastDue: false
};
```
Esse módulo implementa a propriedade `IsPastDue` como stand é como uma instância de timer falsa.

Em seguida, use a extensão de funções VS Code para [criar uma nova função http JavaScript](/azure/javascript/tutorial-vscode-serverless-node-01) e nomeie-a *HttpTrigger*. Depois que a função for criada, adicione um novo arquivo na mesma pasta chamada **index. Test. js**e adicione o seguinte código:

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
A função HTTP do modelo retorna uma cadeia de caracteres "Olá" concatenada com o nome fornecido na cadeia de caracteres de consulta. Esse teste cria uma instância falsa de uma solicitação e a passa para a função HTTP. O teste verifica se o método de *log* é chamado uma vez e o texto retornado é igual a "Hello Bill".

Em seguida, use a extensão de funções VS Code para criar uma nova função de temporizador JavaScript e nomeie-a *TimerTrigger*. Depois que a função for criada, adicione um novo arquivo na mesma pasta chamada **index. Test. js**e adicione o seguinte código:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
A função de temporizador do modelo registra em log uma mensagem no final do corpo da função. Esse teste garante que a função de *log* seja chamada uma vez.

### <a name="run-tests"></a>Executar testes
Para executar os testes, pressione **Ctrl + ~** para abrir a janela de comando e execute `npm test`:

```bash
npm test
```

![Testando Azure Functions com JavaScript em VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Depurar testes

Para depurar seus testes, adicione a configuração a seguir ao arquivo *Launch. JSON* :

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

Em seguida, defina um ponto de interrupção em seu teste e pressione **F5**.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu a escrever testes automatizados para suas funções, continue com estes recursos:
- [Executar manualmente uma função não disparada por HTTP](./functions-manually-run-non-http.md)
- [Tratamento de erro Azure Functions](./functions-bindings-error-pages.md)
- [Depuração local do gatilho de grade de eventos do Azure function](./functions-debug-event-grid-trigger-local.md)
