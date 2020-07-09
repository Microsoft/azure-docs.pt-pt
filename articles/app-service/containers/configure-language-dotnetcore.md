---
title: Configurar aplicações core de ASP.NET Linux
description: Saiba como configurar um recipiente core de ASP.NET pré-construído para a sua aplicação. Este artigo mostra as tarefas de configuração mais comuns.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: e009f5b1fc656f700b3f0e76dda6e545aed535d2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84905770"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Configure uma aplicação Linux ASP.NET Core para o Azure App Service

ASP.NET as aplicações Core devem ser implementadas como binários compilados. A ferramenta de publicação visual Studio constrói a solução e, em seguida, implanta os binários compilados diretamente, enquanto o motor de implementação do Serviço de Aplicações implementa primeiro o repositório de código e, em seguida, compila os binários.

Este guia fornece conceitos e instruções fundamentais para ASP.NET desenvolvedores do Core que usam um recipiente Linux incorporado no Serviço de Aplicações. Se nunca usou o Azure App Service, siga primeiro o [ASP.NET Core quickstart](quickstart-dotnetcore.md) e [ASP.NET Core com o tutorial SQL Database.](tutorial-dotnetcore-sqldb-app.md)

## <a name="show-net-core-version"></a>Mostrar a versão .NET Core

Para mostrar a versão atual .NET Core, executar o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões suportadas .NET Core, executar o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Definir versão .NET Core

Executar o seguinte comando na [Cloud Shell](https://shell.azure.com) para definir a versão .NET Core para 2.1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="customize-build-automation"></a>Personalize a automatização de construção

Se implementar a sua aplicação utilizando pacotes Git ou zip com automatização de construção ligada, o Serviço de Aplicações constrói passos de automação através da seguinte sequência:

1. Executar script personalizado se especificado por `PRE_BUILD_SCRIPT_PATH` .
1. Corra `dotnet restore` para restaurar as dependências do NuGet.
1. Correr `dotnet publish` para construir um binário para a produção.
1. Executar script personalizado se especificado por `POST_BUILD_SCRIPT_PATH` .

`PRE_BUILD_COMMAND`e `POST_BUILD_COMMAND` são variáveis ambientais que estão vazias por defeito. Para executar comandos pré-construção, defina `PRE_BUILD_COMMAND` . Para executar comandos pós-construção, defina `POST_BUILD_COMMAND` .

O exemplo a seguir especifica as duas variáveis a uma série de comandos, separados por vírgulas.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Para variáveis ambientais adicionais para personalizar a automatização de construção, consulte [a configuração oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Para obter mais informações sobre como o App Service funciona e constrói ASP.NET aplicações Core em Linux, consulte [a documentação do Oryx: Como são detetadas e construídas aplicações .NET Core](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md).

## <a name="access-environment-variables"></a>Aceder a variáveis de ambiente

No Serviço de Aplicações, pode [definir definições](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) de aplicações fora do seu código de aplicação. Em seguida, pode acessá-los em qualquer classe usando o padrão ASP.NET padrão de injeção de dependência do núcleo:

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

## <a name="get-detailed-exceptions-page"></a>Obtenha página detalhada de exceções

Quando a sua aplicação ASP.NET gera uma exceção no depurar do Visual Studio, o navegador apresenta uma página de exceção detalhada, mas no Serviço de Aplicações essa página é substituída por um erro genérico **HTTP 500** ou ocorreu um erro durante o **processamento do seu pedido.** . Para exibir a página de exceção detalhada no Serviço de Aplicações, Adicione a definição da `ASPNETCORE_ENVIRONMENT` aplicação à sua aplicação executando o seguinte comando na <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

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

## <a name="deploy-multi-project-solutions"></a>Implementar soluções multi-projecto

Quando se implanta um repositório ASP.NET para o motor de implantação com um ficheiro *.csproj* no diretório de raiz, o motor implementa o projeto. Quando implementa um repositório ASP.NET com um ficheiro *.sln* no diretório de raiz, o motor escolhe o primeiro Web Site ou Web Application Project que encontra como a aplicação do Serviço de Aplicações. É possível que o motor não escolha o projeto que quer.

Para implementar uma solução multi-projecto, pode especificar o projeto a utilizar no Serviço de Aplicações de duas maneiras diferentes:

### <a name="using-deployment-file"></a>Utilização de .ficheiro de implementação

Adicione um ficheiro *.deployment* à raiz do repositório e adicione o seguinte código:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Usando as definições de aplicativos

No <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell,</a>adicione uma configuração de aplicação à sua aplicação de Serviço de Aplicações executando o seguinte comando CLI. *\<app-name>* *\<resource-group-name>* Substitua, e pelos *\<project-name>* valores adequados.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
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

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-linux-no-h.md)]

Para obter mais informações sobre resolução de problemas ASP.NET aplicações Core no Serviço de Aplicações, consulte [Troubleshoot ASP.NET Core no Azure App Service e IIS](https://docs.microsoft.com/aspnet/core/test/troubleshoot-azure-iis)

## <a name="open-ssh-session-in-browser"></a>SSH aberto no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Tutorial: ASP.NET Core app com base de dados SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Serviço de Aplicações Linux FAQ](app-service-linux-faq.md)
