---
title: Configurar aplicações do ASP.NET Core - serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como configurar aplicações de ASP.NET Core a funcionar no serviço de aplicações do Azure
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: e203877b2bc939c1d7fb9390df39f3e2451d12d3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852317"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Configurar uma Linux aplicação ASP.NET Core para o serviço de aplicações do Azure

Aplicações ASP.NET Core tem de ser implementadas como binários compilados. A ferramenta de publicação do Visual Studio compila a solução e, em seguida, implementa os binários compilados diretamente, ao passo que o motor de implementação do serviço de aplicações implementa o repositório de código primeiro e, em seguida, compila os binários.

Este guia fornece conceitos chave e instruções para ASP.NET Core desenvolvedores que usam um contentor do Linux incorporado no serviço de aplicações. Se nunca tiver utilizado o serviço de aplicações do Azure, siga os [início rápido do ASP.NET Core](quickstart-dotnetcore.md) e [ASP.NET Core com tutorial de base de dados SQL](tutorial-dotnetcore-sqldb-app.md) primeiro.

## <a name="show-net-core-version"></a>Mostrar a versão do .NET Core

Para mostrar a versão atual do .NET Core, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todos com suporte versões de .NET Core, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Definir versão do .NET Core

Execute o seguinte comando [Cloud Shell](https://shell.azure.com) para definir a versão do .NET Core para a versão 2.1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>Aceder a variáveis de ambiente

No serviço de aplicações, pode [configurar definições de aplicação](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) fora do seu código de aplicação. Em seguida, pode acessá-los a usando o padrão do ASP.NET:

```csharp
include Microsoft.Extensions.Configuration;
// retrieve App Service app setting
System.Configuration.ConfigurationManager.AppSettings["MySetting"]
// retrieve App Service connection string
Configuration.GetConnectionString("MyDbConnection")
```

Se configurar uma definição de aplicação com o mesmo nome no serviço de aplicações e, em *Web. config*, o valor do serviço de aplicações tem precedência sobre o valor de Web. config. O valor de Web. config permite depurar a aplicação localmente, mas o valor do serviço de aplicações permite que sua passagem para a aplicação no produto com as definições de produção. Cadeias de ligação funcionam da mesma forma. Dessa forma, pode manter os seus segredos da aplicação fora do seu repositório de código e os valores apropriados de acesso sem alterar o seu código.

## <a name="get-detailed-exceptions-page"></a>Obter a página de exceções detalhadas

Quando a aplicação ASP.NET gera uma exceção no depurador do Visual Studio, o navegador exibirá uma página de exceção detalhada, mas no serviço de aplicações, essa página é substituída por um genérico **HTTP 500** erro ou **um erro ocorreu ao mesmo tempo processar o seu pedido.** mensagem. Para exibir a página de exceção detalhada no serviço de aplicações, adicione a `ASPNETCORE_ENVIRONMENT` definição de aplicação à sua aplicação ao executar o seguinte comando no <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Detetar a sessão HTTPS

No serviço de aplicações [terminação de SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre aos balanceadores de carga de rede, para que todos os pedidos HTTPS à sua aplicação, como solicitações HTTP não criptografadas. Se sua lógica de aplicação tem de saber se o usuário solicita estão encriptados ou não, configure o Middleware de cabeçalhos reencaminhados no *Startup.cs*:

- Configurar o middleware com [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) para encaminhar o `X-Forwarded-For` e `X-Forwarded-Proto` cabeçalhos na `Startup.ConfigureServices`.
- Adicione intervalos de endereços IP privados para as redes conhecidas, para que o middleware confiem que o Balanceador de carga do serviço de aplicações.
- Invocar o [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) método na `Startup.Configure` antes de chamar outros middlewares.

Colocar todos os três elementos em conjunto, seu código terá um aspeto semelhante ao seguinte exemplo:

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

Para obter mais informações, consulte [configurar o ASP.NET Core trabalhar com servidores proxy e Balanceadores de carga](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="deploy-multi-project-solutions"></a>Implementar soluções de multiprojeto

Quando implementa um repositório do ASP.NET para o motor de implementação com um *. csproj* ficheiro no diretório de raiz, o mecanismo implementa o projeto. Quando implementa um repositório ASP.NET com uma *. sln* ficheiro no diretório de raiz, o mecanismo escolhe o primeiro Web Site ou projeto de aplicativo Web achar que a aplicação de serviço de aplicações. É possível para o mecanismo não escolher o projeto desejado.

Para implementar uma solução multiprojeto, pode especificar o projeto para utilizar no serviço de aplicações de duas formas diferentes:

### <a name="using-deployment-file"></a>Utilizar o ficheiro. Deployment

Adicionar uma *. Deployment* de ficheiros para a raiz do repositório e adicione o seguinte código:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Com as definições de aplicação

Na <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>, adicione uma definição de aplicação à sua aplicação de serviço de aplicações, executando o seguinte comando da CLI. Substitua  *\<nome da aplicação >*,  *\<nome de grupo de recursos >*, e  *\<nome do projeto >* com os valores apropriados .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abrir sessão SSH no browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: Aplicação ASP.NET Core com a base de dados SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [FAQ do serviço de aplicações Linux](app-service-linux-faq.md)