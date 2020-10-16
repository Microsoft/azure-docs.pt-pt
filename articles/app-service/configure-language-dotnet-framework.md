---
title: Configurar aplicativos de ASP.NET
description: Saiba como configurar uma aplicação ASP.NET no Azure App Service. Este artigo mostra as tarefas de configuração mais comuns.
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 30fddaec9ca5d0439beadedf7c5ca6b6c7d51d83
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88961708"
---
# <a name="configure-an-aspnet-app-for-azure-app-service"></a>Configure uma aplicação ASP.NET para o Azure App Service

> [!NOTE]
> Para ASP.NET Core, consulte [configurar uma aplicação core ASP.NET para o Serviço de Aplicações Azure](configure-language-dotnetcore.md)

ASP.NET aplicações devem ser implementadas para o Azure App Service como binários compilados. A ferramenta de publicação visual Studio constrói a solução e, em seguida, implanta os binários compilados diretamente, enquanto o motor de implementação do Serviço de Aplicações implementa primeiro o repositório de código e, em seguida, compila os binários.

Este guia fornece conceitos e instruções fundamentais para ASP.NET desenvolvedores. Se nunca usou o Azure App Service, siga primeiro o [ASP.NET quickstart](quickstart-dotnet-framework.md) e [ASP.NET com tutorial sql Database.](app-service-web-tutorial-dotnet-sqldatabase.md)

## <a name="show-supported-net-framework-runtime-versions"></a>Mostrar versões de tempo de execução .NET Framework suportadas

No Serviço de Aplicações, as instâncias do Windows já têm todas as versões de Quadro .NET instaladas. Para mostrar o tempo de execução do Quadro .NET e as versões SDK disponíveis para si, navegue `https://<app-name>.scm.azurewebsites.net/DebugConsole` e execute o comando apropriado na consola baseada no navegador:

Para versões de execução CLR 4 (.Net Framework 4 e acima):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\"
```

A versão mais recente do Quadro .NET pode não estar imediatamente disponível.

Para as versões de execução CLR 2 (.NET Framework 3.5 e abaixo):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\"
```

## <a name="show-current-net-framework-runtime-version"></a>Mostrar a versão atual do quadro .NET

Executar o seguinte comando na [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query netFrameworkVersion
```

É utilizado um valor de `v4.0` meios a versão mais recente do CLR 4 (.NET Framework 4.x). É utilizado um valor de `v2.0` meios de versão CLR 2 (.Net Framework 3.5).

## <a name="set-net-framework-runtime-version"></a>Definir versão de tempo de execução do quadro .NET

Por predefinição, o App Service utiliza a versão mais recente suportada no Quadro .NET para executar a sua aplicação ASP.NET. Para executar a sua aplicação utilizando o Quadro .NET 3.5, executar o seguinte comando na [Cloud Shell](https://shell.azure.com) (v2.0 significa CLR 2):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --net-framework-version v2.0
```

## <a name="access-environment-variables"></a>Aceder a variáveis de ambiente

No Serviço de Aplicações, pode [definir definições](configure-common.md#configure-app-settings) de aplicações e cadeias de ligação fora do seu código de aplicação. Em seguida, pode acessá-los em qualquer classe usando o padrão de ASP.NET padrão:

```csharp
using System.Configuration;
...
// Get an app setting
ConfigurationManager.AppSettings["MySetting"];
// Get a connection string
ConfigurationManager.ConnectionStrings["MyConnection"];
}
```

Se configurar uma configuração de uma aplicação com o mesmo nome no Serviço de Aplicações e em *web.config, *o valor do Serviço de Aplicações tem precedência sobre o valor *web.config.* O valor *web.config* local permite depurar a app localmente, mas o valor do Serviço de Aplicações permite que você execute a aplicação em produto com configurações de produção. As cordas de ligação funcionam da mesma forma. Desta forma, pode manter os segredos da sua aplicação fora do seu repositório de código e aceder aos valores apropriados sem alterar o seu código.

## <a name="deploy-multi-project-solutions"></a>Implementar soluções multi-projecto

Quando uma solução visual Studio inclui vários projetos, o processo de publicação do Visual Studio já inclui a seleção do projeto para implementar. Quando implementa para o motor de implementação do Serviço de Aplicações, como com o Git ou com a implementação ZIP, com a automatização de construção ligada, o motor de implantação do Serviço de Aplicações escolhe o primeiro Web Site ou Web Application Project que encontra como a aplicação do Serviço de Aplicações. Pode especificar qual o projeto que o Serviço de Aplicações deve utilizar especificando a definição da `PROJECT` aplicação. Por exemplo, executar o seguinte na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="get-detailed-exceptions-page"></a>Obtenha página detalhada de exceções

Quando a sua aplicação ASP.NET gera uma exceção no depurar do Visual Studio, o navegador apresenta uma página de exceção detalhada, mas no Serviço de Aplicações essa página é substituída por uma mensagem de erro genérica. Para exibir a página de exceção detalhada no Serviço de Aplicações, abra o ficheiro *Web.config* e adicione o `<customErrors mode="Off"/>` elemento sob o `<system.web>` elemento. Por exemplo:

```xml
<system.web>
    <customErrors mode="Off"/>
</system.web>
```

Reimplantar a sua aplicação com o *Web.config*atualizado. Deve agora ver a mesma página de exceção detalhada.

## <a name="access-diagnostic-logs"></a>Aceder aos registos de diagnósticos

Pode adicionar mensagens de diagnóstico no seu código de aplicação utilizando [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace). Por exemplo: 

```csharp
Trace.TraceError("Record not found!"); // Error trace
Trace.TraceWarning("Possible data loss"); // Warning trace
Trace.TraceInformation("GET /Home/Index"); // Information trace
```

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Criar uma aplicação ASP.NET no Azure com a Base de Dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md)