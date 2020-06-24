---
title: Configurar apps do Windows ASP.NET Core
description: Saiba como configurar uma aplicação Core ASP.NET nas instâncias nativas do Windows do Serviço de Aplicações. Este artigo mostra as tarefas de configuração mais comuns.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 5819fc5b2d6e64d1812dacd88a2a4f840f6e03c5
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84908148"
---
# <a name="configure-a-windows-aspnet-core-app-for-azure-app-service"></a>Configure uma aplicação Core windows ASP.NET para o Azure App Service

> [!NOTE]
> Para ASP.NET em .NET Framework, consulte [configurar uma aplicação ASP.NET para o Azure App Service](configure-language-dotnet-framework.md)

ASP.NET as aplicações Core devem ser implantadas no Azure App Service como binários compilados. A ferramenta de publicação visual Studio constrói a solução e, em seguida, implanta os binários compilados diretamente, enquanto o motor de implementação do Serviço de Aplicações implementa primeiro o repositório de código e, em seguida, compila os binários. Para obter informações sobre aplicações Linux, consulte [configurar uma aplicação Linux ASP.NET Core para o Azure App Service](containers/configure-language-dotnetcore.md).

Este guia fornece conceitos e instruções fundamentais para ASP.NET desenvolvedores do Core. Se nunca usou o Azure App Service, siga primeiro o [ASP.NET quickstart](app-service-web-get-started-dotnet.md) e [ASP.NET Core com o tutorial SQL Database.](app-service-web-tutorial-dotnetcore-sqldb.md)

## <a name="show-supported-net-core-runtime-versions"></a>Mostrar versões de tempo de execução .NET Core suportadas

No Serviço de Aplicações, as instâncias do Windows já têm todas as versões suportadas .NET Core instaladas. Para mostrar as versões .NET Core e SDK disponíveis para si, navegue `https://<app-name>.scm.azurewebsites.net/DebugConsole` para executar e executar o seguinte comando na consola baseada no navegador:

```azurecli-interactive
dotnet --info
```

## <a name="set-net-core-version"></a>Definir versão .NET Core

Desaponte o quadro-alvo no ficheiro do projeto para o seu projeto core ASP.NET. Para obter mais informações, consulte [Selecione a versão .NET Core para utilizar](https://docs.microsoft.com/dotnet/core/versions/selection) na documentação .NET Core.

## <a name="access-environment-variables"></a>Aceder a variáveis de ambiente

No Serviço de Aplicações, pode [definir definições](configure-common.md#configure-app-settings) de aplicações fora do seu código de aplicação. Em seguida, pode acessá-los em qualquer classe usando o padrão ASP.NET padrão de injeção de dependência do núcleo:

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve nested App Service app setting
            var myHierarchicalConfig = _configuration["My:Hierarchical:Config:Data"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Se configurar uma configuração de uma aplicação com o mesmo nome no Serviço de Aplicações e em *appsettings.jsem*, por exemplo, o valor do Serviço de Aplicações tem precedência sobre o *appsettings.jssobre* o valor. O *appsettings.js* local de valor permite depurar a app localmente, mas o valor do Serviço de Aplicações permite que a sua aplicação seja executada em produto com configurações de produção. As cordas de ligação funcionam da mesma forma. Desta forma, pode manter os segredos da sua aplicação fora do seu repositório de código e aceder aos valores apropriados sem alterar o seu código.

> [!NOTE]
> Note que os [dados de configuração hierárquica](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/#hierarchical-configuration-data) *emappsettings.jsé* acedido usando o `:` delimiter que é padrão para .NET Core. Para anular uma configuração hierárquica específica no Serviço de Aplicações, defina o nome de definição da aplicação com o mesmo formato delimitado na tecla. pode executar o seguinte exemplo na [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings My:Hierarchical:Config:Data="some value"
```

## <a name="deploy-multi-project-solutions"></a>Implementar soluções multi-projecto

Quando uma solução visual Studio inclui vários projetos, o processo de publicação do Visual Studio já inclui a seleção do projeto para implementar. Quando implementa para o motor de implementação do Serviço de Aplicações, como com o Git ou com a implementação ZIP, com a automatização de construção ligada, o motor de implantação do Serviço de Aplicações escolhe o primeiro Web Site ou Web Application Project que encontra como a aplicação do Serviço de Aplicações. Pode especificar qual o projeto que o Serviço de Aplicações deve utilizar especificando a definição da `PROJECT` aplicação. Por exemplo, executar o seguinte na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

ASP.NET Core fornece um [fornecedor de registo integrado para o Serviço de Aplicações.](https://docs.microsoft.com/aspnet/core/fundamentals/logging/#azure-app-service) Em *Program.cs* do seu projeto, adicione o fornecedor à sua aplicação através do `ConfigureLogging` método de extensão, como mostra o seguinte exemplo:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {
            logging.AddAzureWebAppDiagnostics();
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Em seguida, pode configurar e gerar registos com o [padrão .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Para obter mais informações sobre resolução de problemas ASP.NET aplicações Core no Serviço de Aplicações, consulte [Troubleshoot ASP.NET Core no Azure App Service e IIS](https://docs.microsoft.com/aspnet/core/test/troubleshoot-azure-iis)

## <a name="get-detailed-exceptions-page"></a>Obtenha página detalhada de exceções

Quando a sua aplicação core ASP.NET gera uma exceção no depurar do Visual Studio, o navegador apresenta uma página de exceção detalhada, mas no Serviço de Aplicações essa página é substituída por um erro genérico **HTTP 500** ou ocorreu um erro durante o **processamento do seu pedido.** . Para exibir a página de exceção detalhada no Serviço de Aplicações, Adicione a definição da `ASPNETCORE_ENVIRONMENT` aplicação à sua aplicação executando o seguinte comando na <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Detetar sessão HTTPS

No Serviço de Aplicações, a [rescisão de SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos equilibristas de carga de rede, pelo que todos os pedidos HTTPS chegam à sua aplicação como pedidos HTTP não encriptados. Se a lógica da sua aplicação precisar de saber se os pedidos do utilizador estão encriptados ou não, configuure o Middleware de Cabeçalhos Reencaminhados em *Startup.cs*:

- Configure o middleware com [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) para encaminhar os `X-Forwarded-For` e `X-Forwarded-Proto` cabeçalhos em `Startup.ConfigureServices` .
- Adicione intervalos de endereços IP privados às redes conhecidas, para que o middleware possa confiar no equilibrador de carga do Serviço de Aplicações.
- Invoque o método [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) `Startup.Configure` antes de ligar para outros middlewares.

Juntando os três elementos, o seu código parece ser o seguinte exemplo:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

Para obter mais informações, consulte [o Configure ASP.NET Core para trabalhar com servidores proxy e equilibradores de carga](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: ASP.NET Core app com base de dados SQL](app-service-web-tutorial-dotnetcore-sqldb.md)
