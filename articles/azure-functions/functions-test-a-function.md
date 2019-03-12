---
title: Testar as Funções do Azure
description: Criar testes automatizados para um C# função no Visual Studio e a função de JavaScript no VS Code
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure funções, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor, teste
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: cshoe
ms.openlocfilehash: d3da5cc9e0eff27fde6bcd503c033db12f49371e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767707"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Estratégias para testar seu código nas funções do Azure

Este artigo demonstra como criar testes automatizados para as funções do Azure. 

Todo o código de teste é recomendada, no entanto pode obter os melhores resultados, concluindo a lógica de uma função e a criação de testes fora da função. Abstrair a lógica de distância limita as linhas de uma função de código e permite que a função para ser o único responsável por chamar outras classes ou módulos. Neste artigo, no entanto, demonstra como criar testes automatizados em relação a um HTTP e a função acionada por temporizador.

O conteúdo que se segue é dividido em duas seções diferentes destinadas a diferentes idiomas e ambientes de destino. Pode aprender a testes de compilação:

- [C#no Visual Studio com xUnit](#c-in-visual-studio)
- [JavaScript no VS Code com Jest](#javascript-in-vs-code)

O repositório de exemplo está disponível no [GitHub](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>C#no Visual Studio
O exemplo a seguir descreve como criar um C# função de aplicação no Visual Studio e executar e testes com [xUnit](https://xunit.github.io).

![Testar as funções do Azure com o C# no Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Configurar

Para configurar o ambiente, criar uma função e testar a aplicação. Os passos seguintes ajudam-o a criar as aplicações e as funções necessárias para suportar os testes:

1. [Criar uma nova aplicação de funções](./functions-create-first-azure-function.md) e designe- *funções*
2. [Criar uma função HTTP a partir do modelo](./functions-create-first-azure-function.md) e o nomeio *HttpTrigger*.
3. [Criar uma função de temporizador a partir do modelo](./functions-create-scheduled-function.md) e o nomeio *TimerTrigger*.
4. [Criar uma aplicação de teste xUnit](https://xunit.github.io/docs/getting-started-dotnet-core) no Visual Studio clicando **ficheiro > novo > projeto > Visual C# > .NET Core > xUnit projeto de teste** e nomeie- *Functions.Test*. 
5. Utilizar o Nuget para adicionar um referências a partir da aplicação de teste para [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/) e [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Referência a *funções* aplicação](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017) partir *Functions.Test* aplicação.

### <a name="create-test-classes"></a>Criar classes de teste

Agora que os aplicativos são criados, pode criar as classes usadas para executar os testes automatizados.

Cada função obtém uma instância da [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) para lidar com o log de mensagens. Alguns testes não inicie as mensagens ou não ter nenhuma preocupação de como o Registro em log é implementado. Outros testes necessário avaliar com sessão iniciadas para determinar se um teste consiste na passagem de mensagens.

O `ListLogger` classe destina-se para implementar o `ILogger` de interface e mantenha premido na lista interna de mensagens para avaliação durante um teste.

**Com o botão direito** sobre o *Functions.Test* aplicativo e selecione **adicionar > classe**, um nome **ListLogger.cs** e introduza o seguinte código:

```csharp
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions.Internal;
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

O `ListLogger` classe implementa os seguintes membros como contratado pelo `ILogger` interface:

- **BeginScope**: Âmbitos Adicionar contexto para seu registro de log. Neste caso, o teste apenas aponta para a instância estática sobre o [NullScope](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.abstractions.internal.nullscope) classe para permitir que o teste funcione.

- **IsEnabled**: Um valor padrão de `false` é fornecido.

- **Registo**: Este método utiliza fornecido `formatter` funcionar para formatar a mensagem e, em seguida, adiciona o texto resultante para o `Logs` coleção.

O `Logs` coleção é uma instância de `List<string>` e é inicializada no construtor.

Em seguida, **com o botão direito** sobre o *Functions.Test* aplicativo e selecione **adicionar > classe**, um nome **LoggerTypes.cs** e introduza o código a seguir:

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
Esta enumeração Especifica o tipo de logger utilizado pelos testes. 

Em seguida, **com o botão direito** sobre o *Functions.Test* aplicativo e selecione **adicionar > classe**, um nome **TestFactory.cs** e introduza o código a seguir:

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
O `TestFactory` classe implementa os seguintes membros:

- **Dados**: Essa propriedade retorna um [IEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) recolha de dados de exemplo. Os pares de chave-valor representam valores que são transmitidas numa cadeia de caracteres de consulta.

- **CreateDictionary**: Esse método aceita um par chave/valor como argumentos e devolve um novo `Dictionary` utilizado para criar `QueryCollection` para representar valores de cadeia de caracteres de consulta.

- **CreateHttpRequest**: Este método cria um pedido HTTP foi inicializado com os parâmetros de cadeia de caracteres de consulta especificada.

- **CreateLogger**: Com base no tipo de agente de log, esse método retorna uma classe logger usada para teste. O `ListLogger` mantém um registro de mensagens anteriormente registadas disponíveis para avaliação em testes.

Em seguida, **com o botão direito** sobre o *Functions.Test* aplicativo e selecione **adicionar > classe**, um nome **FunctionsTests.cs** e introduza o código a seguir:

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
            var response = (OkObjectResult)await HttpTrigger.Run(request, logger);
            Assert.Equal("Hello, Bill", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await HttpTrigger.Run(request, logger);
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

- **Http_trigger_should_return_known_string**: Este teste cria um pedido com a consulta de valores de cadeia de caracteres de `name=Bill` para uma função HTTP e verifica se a resposta esperada é devolvida.

- **Http_trigger_should_return_string_from_member_data**: Este teste usa atributos de xUnit para fornecer dados de exemplo para a função HTTP.

- **Timer_should_log_message**: Este teste cria uma instância de `ListLogger` e o transmite para funções de um temporizador. Assim que a função é executada, o registo é verificado para garantir que a mensagem esperada está presente.

### <a name="run-tests"></a>Executar testes

Para executar os testes, navegue para o **Gerenciador de testes** e clique em **executar todos**.

![Testar as funções do Azure com o C# no Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Depurar testes

Para depurar os testes, defina um ponto de interrupção num teste, navegue para o **Gerenciador de testes** e clique em **executar > executar última depurar**.

## <a name="javascript-in-vs-code"></a>JavaScript no VS Code

O exemplo a seguir descreve como criar uma aplicação de função de JavaScript no VS Code e executar e testar com [Jest](https://jestjs.io). Este procedimento utiliza a [extensão de funções de código de VS](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para criar as funções do Azure.

![Testar as funções do Azure com o JavaScript no VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Configurar

Para configurar o seu ambiente, inicializar uma nova aplicação de node. js numa pasta vazia executando `npm init`.

```bash
npm init -y
```
Em seguida, instale Jest executando o seguinte comando:

```bash
npm i jest
```
Atualizar agora _Package. JSON_ para substituir o comando de teste existente com o seguinte comando:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Criar módulos de teste
Com o projeto foi inicializado, pode criar os módulos utilizados para executar os testes automatizados. Comece por criar uma nova pasta chamada *teste* para manter os módulos de suporte.

Na *teste* pasta adicionar um novo ficheiro, nomeie- **defaultContext.js**e adicione o seguinte código:

```javascript
module.exports = {
    log: jest.fn()
};
```
Este módulo mocks a *log* função para representar o contexto de execução padrão.

Em seguida, adicione um novo ficheiro, atribua o nome **defaultTimer.js**e adicione o seguinte código:

```javascript
module.exports = {
    IsPastDue: false
};
```
Este módulo implementa a `IsPastDue` propriedade para se destacar é como uma instância de temporizador falsa.

Em seguida, utilize a extensão de funções de código VS [criar uma nova função de HTTP de JavaScript](https://code.visualstudio.com/tutorials/functions-extension/getting-started) e o nomeio *HttpTrigger*. Assim que a função é criada, adicione um novo ficheiro na mesma pasta com o nome **index.test.js**e adicione o seguinte código:

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
A função HTTP do modelo devolve uma cadeia "Olá", concatenado com o nome fornecido na cadeia de consulta. Este teste cria uma instância falsa de um pedido e passa-o para a função HTTP. O teste verifica se o *log* método é chamado uma vez e texto devolvido é igual a "Hello Bill".

Em seguida, utilize a extensão de funções de código do VS para criar uma nova função de temporizador de JavaScript e o nomeio *TimerTrigger*. Assim que a função é criada, adicione um novo ficheiro na mesma pasta com o nome **index.test.js**e adicione o seguinte código:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
A função de temporizador do modelo regista uma mensagem no final do corpo da função. Esse teste assegura a *log* função é chamada uma vez.

### <a name="run-tests"></a>Executar testes
Para executar os testes, prima **CTRL + ~** para abrir a janela de comando e executar `npm test`:

```bash
npm test
```

![Testar as funções do Azure com o JavaScript no VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Depurar testes

Para depurar seus testes, adicione a seguinte configuração para seus *Launch* ficheiro:

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "program": "${workspaceRoot}\\node_modules\\jest\\bin\\jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

Em seguida, defina um ponto de interrupção em seu teste e prima **F5**.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como escrever testes automatizados para as suas funções, continue com estes recursos:
- [Executar manualmente uma função não acionada por HTTP](./functions-manually-run-non-http.md)
- [Tratamento de erros de funções do Azure](./functions-bindings-error-pages.md)
- [Função do Azure Event Grid acionador Local depuração](./functions-debug-event-grid-trigger-local.md)
