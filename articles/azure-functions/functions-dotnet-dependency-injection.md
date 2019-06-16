---
title: Usar injeção de dependência nas funções do .NET do Azure
description: Aprenda a usar injeção de dependência para registar e utilizar os serviços em funções de .NET
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: funções do Azure, funções, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/28/2019
ms.author: jehollan, glenga, cshoe
ms.openlocfilehash: b1a6751f0d788c26af60b28eee994dc9b3877f00
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66693262"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Usar injeção de dependência nas funções do .NET do Azure

As funções do Azure suporta o padrão de design do software de dependência (injeção), a que é uma técnica para alcançar [inversão de controle (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e as respetivas dependências.

As funções do Azure baseia-se sobre os recursos de Injeção de dependência do ASP.NET Core. Esteja ciente de serviços, tempos de vida e padrões de design do que [injeção de dependência do ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) antes de utilizar recursos de injeção de dependência numa das funções do Azure é recomendável a aplicação.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder utilizar injeção de dependência, tem de instalar os seguintes pacotes NuGet:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Pacote de Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) versão 1.0.28 ou posterior

## <a name="register-services"></a>Registar serviços

Para registar os serviços, pode criar um método para configurar e adicionar componentes para um `IFunctionsHostBuilder` instância.  O anfitrião de funções do Azure cria uma instância de `IFunctionsHostBuilder` e transmite-os diretamente em seu método.

Para registar o método, adicione o `FunctionsStartup` atributo de assembly que especifica o nome do tipo usado durante o arranque.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
            builder.Services.AddSingleton((s) => {
                return new CosmosClient(Environment.GetEnvironmentVariable("COSMOSDB_CONNECTIONSTRING"));
            });
            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

## <a name="use-injected-dependencies"></a>Utilizar injetadas dependências

ASP.NET Core utiliza injeção de construtor para disponibilizar as suas dependências à sua função. O exemplo seguinte demonstra como o `IMyService` e `HttpClient` dependências forem injetadas numa função acionada por HTTP.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();;
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

A utilização de injeção de construtor significa que não devem usar funções estáticas se pretender tirar partido de injeção de dependência.

## <a name="service-lifetimes"></a>Tempos de vida do serviço

Aplicações de funções do Azure fornecem as mesma durações de serviço como [Injeção de dependência ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes): transitório, âmbito e singleton.

Uma aplicação de funções do Azure, uma duração de âmbito do serviço corresponde a uma duração de execução de função. Serviços de âmbito são criados uma vez por execução. Solicitações posteriores para esse serviço durante a execução reutilizar a instância de serviço existente. Um tempo de vida do serviço de singleton corresponde ao tempo de vida do host e é reutilizado em execuções de função nessa instância.

Serviços de tempo de vida de singleton são recomendados para ligações e os clientes, por exemplo `SqlConnection`, `CloudBlobClient`, ou `HttpClient` instâncias.

Ver ou transferir uma [exemplo de durações de serviço diferentes](https://aka.ms/functions/di-sample) no GitHub.

## <a name="logging-services"></a>Serviços de registo

Se precisar de seu próprio provedor de log, a forma recomendada é registrar um `ILoggerProvider` instância. O Application Insights é adicionado automaticamente por funções do Azure.

> [!WARNING]
> Não adicione `AddApplicationInsightsTelemetry()` para a coleção de serviços como ele registradores dos serviços desse conflito com os serviços fornecidos pelo ambiente.

## <a name="function-app-provided-services"></a>Serviços de aplicações fornecido de função

O anfitrião de função registra vários serviços. Os serviços seguintes estão seguros como uma dependência em seu aplicativo:

|Tipo de Serviço|Tempo de vida|Descrição|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|singleton|Configuração de tempo de execução|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|singleton|Responsável por fornecer o ID da instância do host|

Se existirem outros serviços que pretende criar uma dependência, [criar um problema e propor-los no GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Substituição de hospedar serviços

Substituir serviços fornecidos pelo host não é atualmente suportada.  Se existirem serviços que pretende substituir, [criar um problema e propor-los no GitHub](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte os seguintes recursos:

- [Como monitorizar a sua aplicação function app](functions-monitoring.md)
- [Melhores práticas para as funções](functions-best-practices.md)
