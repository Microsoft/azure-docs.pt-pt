---
title: Configurar aplicativos ASP.NET Core-Azure App serviço | Microsoft Docs
description: Saiba como configurar ASP.NET Core aplicativos para trabalharem no serviço Azure App
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.author: cephalin
ms.openlocfilehash: b05120148d3b82829c465effbcdc948da950aaf0
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990267"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Configurar um aplicativo de ASP.NET Core do Linux para Azure App Service

ASP.NET Core aplicativos devem ser implantados como binários compilados. A ferramenta de publicação do Visual Studio cria a solução e implanta os binários compilados diretamente, enquanto o mecanismo de implantação do serviço de aplicativo implanta o repositório de código primeiro e, em seguida, compila os binários.

Este guia fornece os principais conceitos e instruções para os desenvolvedores de ASP.NET Core que usam um contêiner do Linux interno no serviço de aplicativo. Se você nunca usou Azure App serviço, siga o tutorial [ASP.NET Core início rápido](quickstart-dotnetcore.md) e [ASP.NET Core com Banco de dados SQL](tutorial-dotnetcore-sqldb-app.md) primeiro.

## <a name="show-net-core-version"></a>Mostrar versão do .NET Core

Para mostrar a versão atual do .NET Core, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões do .NET Core com suporte, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Definir versão do .NET Core

Execute o seguinte comando na [Cloud Shell](https://shell.azure.com) para definir a versão do .NET Core como 2,1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>Variáveis de ambiente de acesso

No serviço de aplicativo, você pode [definir configurações de aplicativo](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) fora do seu código do aplicativo. Em seguida, você pode acessá-los em qualquer classe usando o padrão de injeção de dependência de ASP.NET Core padrão:

```csharp
include Microsoft.Extensions.Configuration;

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

Se você definir uma configuração de aplicativo com o mesmo nome no serviço de aplicativo e em *appSettings. JSON*, por exemplo, o valor do serviço de aplicativo terá precedência sobre o valor *appSettings. JSON* . O valor local *appSettings. JSON* permite depurar o aplicativo localmente, mas o valor do serviço de aplicativo permite que você execute o aplicativo no produto com as configurações de produção. As cadeias de conexão funcionam da mesma maneira. Dessa forma, você pode manter os segredos do aplicativo fora do seu repositório de código e acessar os valores apropriados sem alterar seu código.

## <a name="get-detailed-exceptions-page"></a>Página obter exceções detalhadas

Quando seu aplicativo ASP.NET gera uma exceção no depurador do Visual Studio, o navegador exibe uma página de exceção detalhada, mas no serviço de aplicativo essa página é substituída por um erro genérico **HTTP 500** ou **ocorreu um erro ao processar sua solicitação.** Mensagem. Para exibir a página de exceção detalhada no serviço de aplicativo, `ASPNETCORE_ENVIRONMENT` adicione a configuração do aplicativo ao seu aplicativo executando o comando a seguir no <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Detectar sessão HTTPS

No serviço de aplicativo, a [terminação SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos balanceadores de carga de rede, portanto, todas as solicitações HTTPS atingem seu aplicativo como solicitações HTTP não criptografadas. Se a lógica do aplicativo precisar saber se as solicitações do usuário estão criptografadas ou não, configure o middleware de cabeçalhos encaminhados em *Startup.cs*:

- Configure o middleware com [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) para encaminhar os `X-Forwarded-For` cabeçalhos `X-Forwarded-Proto` e em `Startup.ConfigureServices`.
- Adicione intervalos de endereços IP privados às redes conhecidas, para que o middleware possa confiar no balanceador de carga do serviço de aplicativo.
- Invoque o método [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) no `Startup.Configure` antes de chamar outros middleware.

Colocando todos os três elementos juntos, seu código é semelhante ao exemplo a seguir:

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

Para obter mais informações, consulte [configurar ASP.NET Core para trabalhar com servidores proxy e balanceadores de carga](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="deploy-multi-project-solutions"></a>Implantar soluções de vários projetos

Quando você implanta um repositório ASP.NET no mecanismo de implantação com um arquivo *. csproj* no diretório raiz, o mecanismo implanta o projeto. Quando você implanta um repositório ASP.NET com um arquivo *. sln* no diretório raiz, o mecanismo escolhe o primeiro site da Web ou o projeto de aplicativo Web que ele encontra como o aplicativo do serviço de aplicativo. É possível que o mecanismo não escolha o projeto desejado.

Para implantar uma solução de vários projetos, você pode especificar o projeto a ser usado no serviço de aplicativo de duas maneiras diferentes:

### <a name="using-deployment-file"></a>Usando arquivo. Deployment

Adicione um arquivo *. Deployment* à raiz do repositório e adicione o seguinte código:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Usando configurações do aplicativo

No <a target="_blank" href="https://shell.azure.com">Azure cloud Shell</a>, adicione uma configuração de aplicativo ao aplicativo do serviço de aplicativo executando o comando da CLI a seguir. *Substitua\<app-Name >* ,  *\<Resource-Group-Name >* e  *\<Project-Name >* pelos valores apropriados.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abrir sessão SSH no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: ASP.NET Core aplicativo com o banco de dados SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Perguntas frequentes sobre o serviço de aplicativo Linux](app-service-linux-faq.md)