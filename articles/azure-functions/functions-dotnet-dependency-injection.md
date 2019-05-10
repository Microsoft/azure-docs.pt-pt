---
title: Usar injeção de dependência nas funções do .NET do Azure
description: Aprenda a usar injeção de dependência para registar e utilizar os serviços em funções de .NET
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: funções do Azure, funções, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2019
ms.author: jehollan
ms.openlocfilehash: 2044718d2ec7a7acc58e1e7ba9ba04ec5caf16b3
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408454"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Usar injeção de dependência nas funções do .NET do Azure

As funções do Azure suporta o padrão de design do software de dependência (injeção), a que é uma técnica para alcançar [inversão de controle (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) entre classes e as respetivas dependências.

As funções do Azure baseia-se sobre os recursos de Injeção de dependência do ASP.NET Core.  Deve compreender os serviços, tempos de vida e padrões de design de [injeção de dependência do ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) antes de usá-las em funções.

## <a name="installing-dependency-injection-packages"></a>Instalar pacotes de injeção de dependência

Para utilizar as funcionalidades de injeção de dependência, terá de incluir o pacote do NuGet que expõe essas APIs.

```powershell
Install-Package Microsoft.Azure.Functions.Extensions
```

## <a name="registering-services"></a>Registrando serviços

Para registar os serviços, pode criar um método de configurar e adicionar componentes para um `IFunctionsHostBuilder` instância.  O anfitrião de funções do Azure cria um `IFunctionsHostBuilder` e transmite-os diretamente no seu método configurado.

Para registar a configurar o método, tem de adicionar um atributo de assembly que especifica o tipo para seu configurar usando o método o `FunctionsStartup` atributo.

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

## <a name="service-lifetimes"></a>Tempos de vida do serviço

Aplicações de funções do Azure fornecem as mesma durações de serviço como [Injeção de dependência ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes), transitório, âmbito e singleton.

Uma aplicação de funções, uma duração de âmbito do serviço corresponde a uma duração de execução de função. Serviços de âmbito são criados uma vez por execução.  Solicitações posteriores para esse serviço durante a execução reutilizar essa instância.  Um tempo de vida do serviço de singleton corresponde ao tempo de vida do host e é reutilizado em execuções de função nessa instância.

Serviços de tempo de vida de singleton são recomendados para ligações e os clientes, por exemplo um `SqlConnection`, `CloudBlobClient`, ou `HttpClient`.

Ver ou transferir uma [exemplo de durações de serviço diferentes](https://aka.ms/functions/di-sample).

## <a name="logging-services"></a>Serviços de registo

Se precisar de seu próprio provedor de log, a forma recomendada é registrar um `ILoggerProvider`.  Para o Application Insights, as funções adiciona o Application Insights automaticamente para.  

> [!WARNING]
> Não adicione `AddApplicationInsightsTelemetry()` para os serviços de recolha conforme ela Registre a serviços que entram em conflito com o que é fornecida pelo ambiente. 
 
## <a name="function-app-provided-services"></a>Serviços de aplicações fornecido de função

O anfitrião de função registrará muitos serviços em si.  Seguem-se os serviços que são seguros criar uma dependência.  Outros serviços do anfitrião não são suportados para registar ou dependem.  Se existirem outros serviços que pretende criar uma dependência, tente [criar um problema e a discussão no GitHub](https://github.com/azure/azure-functions-host).

|Tipo de Serviço|Tempo de vida|Descrição|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|singleton|Configuração de tempo de execução|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|singleton|Responsável por fornecer o ID da instância do host|

### <a name="overriding-host-services"></a>Substituição de hospedar serviços

Substituir serviços fornecidos pelo host não é atualmente suportada.  Se existirem serviços que pretende substituir, [criar um problema e a discussão no GitHub](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte os seguintes recursos:

* [Como monitorizar a sua aplicação function app](functions-monitoring.md)
* [Melhores práticas para as funções](functions-best-practices.md)