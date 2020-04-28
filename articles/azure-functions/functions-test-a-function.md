---
title: Testar as Funções do Azure
description: Criar testes automatizados para uma função C# no Estúdio Visual e Função JavaScript no Código VS
author: craigshoemaker
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: a37fd886e1bc70226b2e54750540dfcb79ee5973
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75768882"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Estratégias para testar o seu código nas Funções do Azure

Este artigo demonstra como criar testes automatizados para funções azure. 

O teste de todo o código é recomendado, no entanto pode obter os melhores resultados, encerrando a lógica de uma Função e criando testes fora da Função. A abstração da lógica de afastamento limita as linhas de código de uma Função e permite que a Função seja a única responsável por chamar outras classes ou módulos. Este artigo, no entanto, demonstra como criar testes automatizados contra um HTTP e funções acionadas pelo temporizador.

O conteúdo que se segue é dividido em duas secções diferentes destinadas a direcionar diferentes línguas e ambientes. Pode aprender a fazer testes em:

- [C# em Estúdio Visual com xUnit](#c-in-visual-studio)
- [JavaScript em código VS com Jest](#javascript-in-vs-code)

O repositório da amostra está disponível no [GitHub](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>C# no Estúdio Visual
O exemplo seguinte descreve como criar uma aplicação de função C# no Estúdio Visual e executar e testar com [xUnit](https://xunit.github.io).

![Testar funções azure com C# em Estúdio Visual](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Configuração

Para configurar o seu ambiente, crie uma aplicação de Função e teste. Os seguintes passos ajudam a criar as aplicações e funções necessárias para suportar os testes:

1. [Criar uma nova app Funções](./functions-create-first-azure-function.md) e nomeá-lo *Funções*
2. [Crie uma função HTTP a partir do modelo](./functions-create-first-azure-function.md) e nomeie-o *HttpTrigger*.
3. [Crie uma função temporizador a partir do modelo](./functions-create-scheduled-function.md) e nomeie-o *TimerTrigger*.
4. [Crie uma aplicação xUnit Test](https://xunit.github.io/docs/getting-started-dotnet-core) no Estúdio Visual clicando em **File > New > Project > Visual C# > .NET Core > xUnit Test Project** e nomeá-lo *Functions.Test*. 
5. Use nuGet para adicionar uma referência da aplicação de teste para [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Consulte a aplicação *Funções* ](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017) a partir de *Funções.Teste* aplicação.

### <a name="create-test-classes"></a>Criar aulas de teste

Agora que as aplicações são criadas, pode criar as classes usadas para executar os testes automatizados.

Cada função requer uma instância do [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) para lidar com o registo de mensagens. Alguns testes ou não registam mensagens ou não têm qualquer preocupação com a forma como o registo é implementado. Outros testes precisam avaliar as mensagens registadas para determinar se um teste está a passar.

A `ListLogger` classe implementa a `ILogger` interface e detém uma lista interna de mensagens para avaliação durante um teste.

**Clique à direita** na aplicação *Functions.Teste* e selecione **Adicionar > Class,** nomeá-lo **NullScope.cs** e introduzir o seguinte código:

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

Em seguida, **clique à direita** na aplicação *Funções.Teste* e selecione **Adicionar > Classe,** nomeie-o **ListLogger.cs** e introduza o seguinte código:

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

A `ListLogger` classe implementa os seguintes `ILogger` membros contratados pela interface:

- **BeginScope**: Os âmbitos adicionam contexto à sua exploração madeireira. Neste caso, o teste apenas aponta `NullScope` para a instância estática da classe para permitir que o teste funcione.

- **IsEnabled**: `false` É fornecido um valor predefinido.

- **Início do registo**: `formatter` Este método utiliza a função fornecida `Logs` para formatar a mensagem e, em seguida, adiciona o texto resultante à recolha.

A `Logs` coleção é `List<string>` um exemplo de e é inicializada no construtor.

Em seguida, **clique à direita** na aplicação *Funções.Teste* e selecione **Adicionar > Classe,** nomeie-o **LoggerTypes.cs** e introduza o seguinte código:

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
Esta enumeração especifica o tipo de madeireiro utilizado pelos testes. 

Em seguida, **clique à direita** na aplicação *Funções.Teste* e selecione **Adicionar > Classe,** nomeie-o **TestFactory.cs** e introduza o seguinte código:

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
A `TestFactory` classe implementa os seguintes membros:

- **Dados**: Esta propriedade devolve uma recolha [IEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) de dados da amostra. Os pares de valor-chave representam valores que são passados para uma corda de consulta.

- **CreateDictionary**: Este método aceita um par chave/valor `Dictionary` como argumentos `QueryCollection` e devolve um novo usado para criar para representar valores de cordas de consulta.

- **CreateHttpRequest**: Este método cria um pedido HTTP inicializado com os parâmetros de corda de consulta dada.

- **CreateLogger**: Com base no tipo de logger, este método devolve uma classe de logger utilizada para o teste. O `ListLogger` registo de mensagens registadas disponíveis para avaliação em testes.

Em seguida, **clique à direita** na aplicação *Funções.Teste* e selecione **Adicionar > Class,** nomeá-lo **FunctionsTests.cs** e introduzir o seguinte código:

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
Os membros implementados nesta classe são:

- **Http_trigger_should_return_known_string**: Este teste cria um pedido `name=Bill` com os valores de cadeia de consulta de uma função HTTP e verifica se a resposta esperada é devolvida.

- **Http_trigger_should_return_string_from_member_data**: Este teste utiliza atributos xUnit para fornecer dados da amostra à função HTTP.

- **Timer_should_log_message**: Este teste `ListLogger` cria uma instância e passa-o para funções temporizadoras. Uma vez executada a função, o registo é verificado para garantir que a mensagem esperada está presente.

Se pretender aceder às definições de aplicações nos seus testes, pode utilizar [system.environment.GetEnvironmentVariable](./functions-dotnet-class-library.md#environment-variables).

### <a name="run-tests"></a>Realizar testes

Para executar os testes, navegue para o Explorador de **Teste** e clique em **Executar tudo**.

![Testar funções azure com C# em Estúdio Visual](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Testes de depuração

Para depurar os testes, detete um ponto de rutura num teste, navegue para o **Test Explorer** e clique em Executar **> Debug Last Run**.

## <a name="javascript-in-vs-code"></a>JavaScript no Código VS

O exemplo seguinte descreve como criar uma aplicação JavaScript Function no Código VS e executar e testar com [Jest](https://jestjs.io). Este procedimento utiliza a [extensão funções](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) do código VS para criar funções Azure.

![Testar funções azure com JavaScript no código VS](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Configuração

Para configurar o seu ambiente, inicie uma nova aplicação `npm init`Node.js numa pasta vazia executando .

```bash
npm init -y
```
Em seguida, instale o Jest executando o seguinte comando:

```bash
npm i jest
```
Agora atualize _o pacote.json_ para substituir o comando de teste existente pelo seguinte comando:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Criar módulos de teste
Com o projeto inicializado, pode criar os módulos utilizados para executar os testes automatizados. Comece por criar uma nova pasta chamada *teste* para manter os módulos de suporte.

Na pasta de *teste* adicione um novo ficheiro, nomeie-o **padrãoContext.js**, e adicione o seguinte código:

```javascript
module.exports = {
    log: jest.fn()
};
```
Este módulo ridiculariza a função de *registo* para representar o contexto de execução padrão.

Em seguida, adicione um novo ficheiro, nomeie-o **predefinidoTimer.js**, e adicione o seguinte código:

```javascript
module.exports = {
    IsPastDue: false
};
```

Este módulo implementa a `IsPastDue` propriedade para ficar é como uma instância de temporizador falso. Configurações temporizadoras como expressões NCRONTAB não são necessárias aqui, uma vez que o arnês de teste está simplesmente chamando a função diretamente para testar o resultado.

Em seguida, utilize a extensão de funções de código VS para [criar uma nova função JavaScript HTTP](/azure/javascript/tutorial-vscode-serverless-node-01) e nomeá-la *HttpTrigger*. Assim que a função for criada, adicione um novo ficheiro na mesma pasta chamada **index.test.js**e adicione o seguinte código:

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
A função HTTP do modelo devolve uma série de "Olá" concatenado com o nome fornecido na corda de consulta. Este teste cria uma instância falsa de um pedido e passa-o para a função HTTP. O teste verifica que o método de *registo* é chamado uma vez e o texto devolvido é igual a "Hello Bill".

Em seguida, utilize a extensão de funções de código VS para criar uma nova função do temporizador JavaScript e nomeá-lo *TimerTrigger*. Assim que a função for criada, adicione um novo ficheiro na mesma pasta chamada **index.test.js**e adicione o seguinte código:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
A função temporizador do modelo regista uma mensagem no final do corpo da função. Este teste garante que a função de *registo* é chamada uma vez.

### <a name="run-tests"></a>Realizar testes
Para eexecutar os testes, prima **CTRL +** ~ `npm test`para abrir a janela de comando e executar:

```bash
npm test
```

![Testar funções azure com JavaScript no código VS](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Testes de depuração

Para desinserir os seus testes, adicione a seguinte configuração ao ficheiro *launch.json:*

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

Em seguida, detete um ponto de rutura no seu teste e prima **F5**.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a escrever testes automatizados para as suas funções, continue com estes recursos:
- [Executar manualmente uma função não acionada por HTTP](./functions-manually-run-non-http.md)
- [Manipulação de erros das Funções Azure](./functions-bindings-error-pages.md)
- [Rede de eventos de função azure gatilho depuração local](./functions-debug-event-grid-trigger-local.md)
