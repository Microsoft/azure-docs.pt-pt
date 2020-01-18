---
title: Usar injeção de dependência no .NET Azure Functions
description: Saiba como usar a injeção de dependência para registrar e usar serviços em funções do .NET
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: a17ff15e71251e781cd30c33a5616af85e4f4eb9
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76260088"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Usar injeção de dependência no .NET Azure Functions

Azure Functions dá suporte ao padrão de design de software injeção de dependência (DI), que é uma técnica para obter [inversão de controle (IOC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e suas dependências.

- A injeção de dependência no Azure Functions é criada sobre os recursos de injeção de dependência do .NET Core. É recomendável ter familiaridade com a [injeção de dependência do .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) . No entanto, há diferenças em como você substitui dependências e como os valores de configuração são lidos com Azure Functions no plano de consumo.

- O suporte para injeção de dependência começa com Azure Functions 2. x.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder usar a injeção de dependência, você deve instalar os seguintes pacotes NuGet:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Pacote Microsoft. net. Sdk. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) versão 1.0.28 ou posterior

## <a name="register-services"></a>Registrar serviços

Para registrar serviços, crie um método para configurar e adicionar componentes a uma instância de `IFunctionsHostBuilder`.  O host Azure Functions cria uma instância de `IFunctionsHostBuilder` e a transmite diretamente para o seu método.

Para registrar o método, adicione o atributo `FunctionsStartup` assembly que especifica o nome de tipo usado durante a inicialização.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### <a name="caveats"></a>Limitações

Uma série de etapas de registro executada antes e depois que o tempo de execução processa a classe de inicialização. Portanto, tenha em mente os seguintes itens:

- *A classe de inicialização destina-se apenas à instalação e ao registro.* Evite usar serviços registrados na inicialização durante o processo de inicialização. Por exemplo, não tente registrar uma mensagem em um agente que está sendo registrado durante a inicialização. Esse ponto do processo de registro é muito cedo para que os serviços estejam disponíveis para uso. Depois que o método de `Configure` é executado, o tempo de execução do Functions continua registrando dependências adicionais, o que pode afetar o funcionamento dos serviços.

- *O contêiner de injeção de dependência só mantém tipos explicitamente registrados*. Os únicos serviços disponíveis como tipos que podem ser injetados são os que são configurados no método `Configure`. Como resultado, tipos específicos de funções como `BindingContext` e `ExecutionContext` não estão disponíveis durante a instalação ou como tipos injetados.

## <a name="use-injected-dependencies"></a>Usar dependências injetadas

A injeção de construtor é usada para disponibilizar suas dependências em uma função. O uso de injeção de construtor requer que você não use classes estáticas.

O exemplo a seguir demonstra como as dependências de `IMyService` e `HttpClient` são injetadas em uma função disparada por HTTP. Este exemplo usa o pacote [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) necessário para registrar um `HttpClient` na inicialização.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

## <a name="service-lifetimes"></a>Tempos de vida do serviço

Azure Functions aplicativos fornecem os mesmos tempos de vida de serviço como [injeção de dependência ASP.net](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes). Para um aplicativo do functions, os tempos de vida de serviço diferentes se comportam da seguinte maneira:

- **Transitório**: os serviços transitórios são criados após cada solicitação do serviço.
- Com **escopo**: o tempo de vida do serviço com escopo corresponde a um tempo de vida de execução da função. Os serviços com escopo são criados uma vez por execução. Solicitações posteriores para esse serviço durante a execução reutilizam a instância de serviço existente.
- **Singleton**: o tempo de vida do serviço singleton corresponde ao tempo de vida do host e é reutilizado nas execuções de função nessa instância. Os serviços de vida útil singleton são recomendados para conexões e clientes, por exemplo `SqlConnection` ou instâncias de `HttpClient`.

Exiba ou baixe uma [amostra de tempos de vida de serviço diferentes](https://aka.ms/functions/di-sample) no github.

## <a name="logging-services"></a>Serviços de log

Se você precisar de seu próprio provedor de log, registre um tipo personalizado como uma instância de `ILoggerProvider`. Application Insights é adicionado por Azure Functions automaticamente.

> [!WARNING]
> - Não adicione `AddApplicationInsightsTelemetry()` à coleção de serviços, pois ela registra os serviços que entram em conflito com os serviços fornecidos pelo ambiente.
> - Não Registre seu próprio `TelemetryConfiguration` ou `TelemetryClient` se você estiver usando a funcionalidade interna do Application Insights.

## <a name="function-app-provided-services"></a>Serviços fornecidos pelo aplicativo de funções

O host de função registra vários serviços. Os seguintes serviços são seguros para serem adotados como uma dependência em seu aplicativo:

|Tipo de Serviço|Tempo de vida|Descrição|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Único|Configuração de tempo de execução|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Único|Responsável por fornecer a ID da instância do host|

Se houver outros serviços nos quais você deseja assumir uma dependência, [crie um problema e proponha-os no GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Substituindo serviços de host

Atualmente, não há suporte para a substituição de serviços fornecidos pelo host.  Se houver serviços que você deseja substituir, [crie um problema e proponha-os no GitHub](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Trabalhando com opções e configurações

Os valores definidos nas [configurações do aplicativo](./functions-how-to-use-azure-function-app-settings.md#settings) estão disponíveis em uma instância do `IConfiguration`, que permite que você leia os valores das configurações do aplicativo na classe de inicialização.

Você pode extrair valores da instância de `IConfiguration` para um tipo personalizado. Copiar os valores das configurações do aplicativo para um tipo personalizado torna fácil testar seus serviços, tornando esses valores injetados. As configurações lidas na instância de configuração devem ser pares de chave/valor simples.

Considere a seguinte classe que inclui uma propriedade chamada consistente com uma configuração de aplicativo:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

E um arquivo de `local.settings.json` que pode estruturar a configuração personalizada da seguinte maneira:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

De dentro do método `Startup.Configure`, você pode extrair valores da instância `IConfiguration` para seu tipo personalizado usando o seguinte código:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

Chamar `Bind` copia os valores que têm nomes de propriedade correspondentes da configuração na instância personalizada. A instância de opções agora está disponível no contêiner IoC para injetar em uma função.

O objeto options é injetado na função como uma instância da interface de `IOptions` genérica. Use a propriedade `Value` para acessar os valores encontrados em sua configuração.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

Consulte o [padrão de opções em ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) para obter mais detalhes sobre como trabalhar com opções.

> [!WARNING]
> Evite tentar ler valores de arquivos como *local. Settings. JSON* ou *appSettings. { Environment}. JSON* no plano de consumo. Os valores lidos desses arquivos relacionados a conexões de gatilho não estão disponíveis conforme o aplicativo é dimensionado porque a infraestrutura de hospedagem não tem acesso às informações de configuração.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte os seguintes recursos:

- [Como monitorar seu aplicativo de funções](functions-monitoring.md)
- [Práticas recomendadas para funções](functions-best-practices.md)
