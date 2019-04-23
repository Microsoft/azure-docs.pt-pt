---
title: Aplicações de web do ASP.NET Core Azure Linux com o Application Insights Profiler de perfil | Documentos da Microsoft
description: Uma descrição geral conceptual e um tutorial passo a passo sobre como utilizar o Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.reviewer: mbullwin
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: cweining
ms.openlocfilehash: 35789cc1e516fb24d5e985e12b44fe3cd01b795d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790161"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Aplicações web de ASP.NET Core Azure Linux de perfil com o Application Insights Profiler

Esta funcionalidade encontra-se em pré-visualização.

Descubra de quanto tempo é gasto em cada método da sua aplicação web em direto ao usar [Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights Profiler está agora disponível para aplicações web ASP.NET Core que ficam alojadas em Linux no App Service do Azure. Este guia fornece instruções passo a passo sobre como os rastreios do Profiler podem ser coletados para aplicações web do ASP.NET Core Linux.

Depois de concluir este passo a passo, a sua aplicação pode recolher rastreios do Profiler, como os rastreios que são mostrados na imagem. Neste exemplo, o rastreio do Profiler indica que um pedido web específico é lento devido ao tempo gasto aguardando. O *o hot path* no código que está deixando a aplicação é marcada por um ícone de térmica. O **sobre** método na **HomeController** secção está deixando a aplicação web, uma vez que está chamando o método o **Sleep** função.

![Rastreios do Profiler](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Pré-requisitos
As instruções seguintes aplicam-se a todos os ambientes de desenvolvimento Windows, Linux e Mac:

* Instalar o [.NET Core SDK 2.1.2 ou posterior](https://dotnet.microsoft.com/download/archives).
* Instalar o Git, seguindo as instruções em [introdução - instalar o Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>Configurar o projeto localmente

1. Abra uma janela de linha de comandos no seu computador. As seguintes instruções funcionam para todos os ambientes de desenvolvimento Windows, Linux e Mac.

1. Crie uma aplicação web ASP.NET Core MVC:

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

1. Altere o diretório de trabalho para a pasta de raiz para o projeto.

1. Adicione o pacote NuGet para recolher rastreios do Profiler:

    ```shell
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

1. Ative o Application Insights no Program.cs:

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseApplicationInsights() // Add this line of code to Enable Application Insights
            .UseStartup<Startup>();
    ```
    
1. Ative Profiler no Startup.cs:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddServiceProfiler(); // Add this line of code to Enable Profiler
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

1. Adicionar uma linha de código a **HomeController.cs** secção aleatoriamente atrasar alguns segundos:

    ```csharp
        using System.Threading;
        ...

        public IActionResult About()
            {
                Random r = new Random();
                int delay = r.Next(5000, 10000);
                Thread.Sleep(delay);
                return View();
            }
    ```

1. Guarde e consolide as suas alterações no repositório local:

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>Criar a aplicação web do Linux para alojar o seu projeto

1. Crie o ambiente da aplicação web utilizando o serviço de aplicações no Linux:

    ![Criar a aplicação web do Linux](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. Crie as credenciais de implementação:

    > [!NOTE]
    > Registe a sua palavra-passe para utilizar mais tarde quando implementar a sua aplicação web.

    ![Criar as credenciais de implementação](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Escolha as opções de implementação. Configure um repositório de Git local na aplicação web ao seguir as instruções no portal do Azure. É criado automaticamente um repositório de Git.

    ![Configurar o repositório de Git](./media/profiler-aspnetcore-linux/setup-git-repo.png)

Para mais opções de implementação, consulte [este artigo](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type).

## <a name="deploy-your-project"></a>Implementar o seu projeto

1. Na janela do Prompt de comando, navegue para a pasta de raiz para o seu projeto. Adicione um repositório de Git remoto para apontar para o repositório no serviço de aplicações:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Utilize o **nome de utilizador** que utilizou para criar as credenciais de implementação.
    * Utilize o **nome da aplicação** que utilizou para criar a aplicação web com o serviço de aplicações no Linux.

2. Implemente o projeto ao enviar as alterações para o Azure:

    ```
    git push azure master
    ```

Deverá ver um resultado semelhante ao seguinte exemplo:

    ```
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    …

    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Adicionar o Application Insights para monitorizar as suas aplicações web

1. [Criar um recurso do Application Insights](./../../azure-monitor/app/create-new-resource.md ).

2. Copiar o **iKey** valor do recurso do Application Insights e defina as seguintes definições nas suas aplicações web:

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ```

    Quando as definições da aplicação são alteradas, o site é reiniciado automaticamente. Depois das novas definições são aplicadas, o Profiler é executado imediatamente durante dois minutos. O Profiler, em seguida, é executado durante dois minutos por hora.

3. Gere algum tráfego para o seu Web site. Pode gerar tráfego ao atualizar o site **sobre** página algumas vezes.

4. Aguardar dois a cinco minutos para os eventos a agregação para o Application Insights.

5. Navegue para o Application Insights **desempenho** painel no portal do Azure. Pode ver os rastreios do Profiler à direita na parte inferior do painel.

    ![Ver os rastreios do Profiler](./media/profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>Problemas conhecidos

### <a name="profile-now-button-doesnt-work-for-linux-profiler"></a>Botão de agora de perfil não funciona para Profiler do Linux
A versão do Linux do criador de perfil do App Insights ainda não suporta a criação de perfis com o perfil agora botão de pedido.


## <a name="next-steps"></a>Passos Seguintes
Se utilizar contentores personalizadas que são alojadas pelo App Service do Azure, siga as instruções em [ ative Profiler de serviço para uma aplicação ASP.NET Core em contentores](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) para ativar o Application Insights Profiler.

Reportar quaisquer problemas ou sugestões para o repositório do GitHub do Application Insights: [ApplicationInsights-Profiler-AspNetCore: Problemas](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).
