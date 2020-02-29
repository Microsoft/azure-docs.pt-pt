---
title: Configure aplicativos De ASP.NET Core do Linux
description: Aprenda a configurar um recipiente de ASP.NET Core pré-construído para a sua aplicação. Este artigo mostra as tarefas de configuração mais comuns.
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: 30cd6ad1b5516eb3bc7e858ae364a88ace1b93b3
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77917635"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Configure um aplicativo Linux ASP.NET Core para o Azure App Service

ASP.NET aplicações Core devem ser implementadas como binários compilados. A ferramenta de publicação visual Studio constrói a solução e, em seguida, implanta os binários compilados diretamente, enquanto o motor de implementação do Serviço de Aplicações implementa primeiro o repositório de código e depois compila os binários.

Este guia fornece conceitos e instruções fundamentais para ASP.NET desenvolvedores core que usam um recipiente Linux incorporado no Serviço de Aplicações. Se nunca usou o Azure App Service, siga o [ASP.NET Core quickstart](quickstart-dotnetcore.md) e ASP.NET Core com o tutorial de Base de [Dados SQL](tutorial-dotnetcore-sqldb-app.md) primeiro.

## <a name="show-net-core-version"></a>Mostrar a versão .NET Core

Para mostrar a versão atual .NET Core, execute o seguinte comando na [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões suportadas .NET Core, execute o seguinte comando na [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Definir .NET Versão Core

Executar o seguinte comando na [Cloud Shell](https://shell.azure.com) para definir a versão .NET Core para 2.1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="customize-build-automation"></a>Personalize a automatização de construção

Se implementar a sua aplicação utilizando pacotes Git ou zip com automatização de construção ligada, o Serviço de Aplicações constrói passos de automação através da seguinte sequência:

1. Executar script personalizado se especificado por `PRE_BUILD_SCRIPT_PATH`.
1. Executar `dotnet restore` para restaurar as dependências do NuGet.
1. Executar `dotnet publish` para construir um binário para a produção.
1. Executar script personalizado se especificado por `POST_BUILD_SCRIPT_PATH`.

`PRE_BUILD_COMMAND` e `POST_BUILD_COMMAND` são variáveis ambientais que são vazias por defeito. Para executar comandos pré-construção, defina `PRE_BUILD_COMMAND`. Para executar comandos pós-construção, defina `POST_BUILD_COMMAND`.

O exemplo seguinte especifica as duas variáveis a uma série de comandos, separados por vírgulas.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Para variáveis ambientais adicionais para personalizar a automatização de construção, consulte a [configuração oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Para obter mais informações sobre como o App Service funciona e constrói ASP.NET aplicações Core em Linux, consulte [documentação oryx: Como as aplicações .NET Core são detetadas e construídas](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md).

## <a name="access-environment-variables"></a>Aceder a variáveis de ambiente

No Serviço de Aplicações, pode [definir as definições](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) de aplicações fora do seu código de aplicações. Em seguida, você pode acessá-los em qualquer classe usando o padrão padrão ASP.NET padrão de injeção de dependência do Núcleo:

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
            // retrieve App Service app setting
            var myAppSetting = _configuration["MySetting"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Se configurar uma definição de aplicação com o mesmo nome no App Service e em *appsettings.json*, por exemplo, o valor do Serviço de Aplicações tem precedência sobre o valor *appsettings.json.* O valor de *aplicações locais.json* permite-lhe desinsermar a app localmente, mas o valor do Serviço de Aplicações permite que a sua aplicação execute a aplicação em produto com configurações de produção. As cordas de ligação funcionam da mesma forma. Desta forma, pode manter os seus segredos de aplicação fora do seu repositório de código e aceder aos valores apropriados sem alterar o seu código.

## <a name="get-detailed-exceptions-page"></a>Obtenha página de exceções detalhadas

Quando a sua aplicação ASP.NET gera uma exceção no debugger do Estúdio Visual, o navegador apresenta uma página de exceção detalhada, mas no App Service essa página é substituída por um erro genérico **http 500** ou um erro ocorreu durante o **processamento do seu pedido.** Mensagem. Para exibir a página de exceção detalhada no Serviço de Aplicações, adicione a definição de aplicação `ASPNETCORE_ENVIRONMENT` à sua aplicação executando o seguinte comando na <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Detetar sessão HTTPS

No Serviço de Aplicações, a rescisão do [SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos equilibradores de carga da rede, pelo que todos os pedidos HTTPS chegam à sua aplicação como pedidos HTTP não encriptados. Se a lógica da sua aplicação necessitar de saber se os pedidos do utilizador estão encriptados ou não, configure o Middleware De Cabeçalhos Reencaminhados em *Startup.cs:*

- Configure o middleware com [ForwardheadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) para encaminhar os cabeçalhos de `X-Forwarded-For` e `X-Forwarded-Proto` em `Startup.ConfigureServices`.
- Adicione gamas de endereços IP privados às redes conhecidas, para que o middleware possa confiar no equilibrador de carga do Serviço de Aplicações.
- Invoque o método [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) em `Startup.Configure` antes de chamar outros middlewares.

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

Para mais informações, consulte [configure ASP.NET Core para trabalhar com servidores proxy e equilibradores](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer)de carga .

## <a name="deploy-multi-project-solutions"></a>Implementar soluções multi-projeto

Quando se implementa um ASP.NET repositório para o motor de implantação com um ficheiro *.csproj* no diretório raiz, o motor implanta o projeto. Ao implementar um ASP.NET repositório com um ficheiro *.sln* no diretório raiz, o motor escolhe o primeiro Web Site ou Web Application Project que encontra como app app Service. É possível que o motor não escolha o projeto que quer.

Para implementar uma solução de vários projetos, pode especificar o projeto a utilizar no App Service de duas formas diferentes:

### <a name="using-deployment-file"></a>Usando o ficheiro .deployment

Adicione um ficheiro *.de implantação* à raiz do repositório e adicione o seguinte código:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Usando as definições de aplicativos

No <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell,</a>adicione uma definição de aplicação à sua app Service executando o seguinte comando CLI. Substitua *\<nome de aplicativo>* \<nome *de grupo de recursos>* e *\<nome de projeto>* com os valores apropriados.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abra a sessão sSH no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: ASP.NET aplicação Core com Base de Dados SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Serviço de aplicações Linux FAQ](app-service-linux-faq.md)
