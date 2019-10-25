---
title: Perfil ASP.NET Core aplicativos Web Linux do Azure com Application Insights Profiler | Microsoft Docs
description: Uma visão geral conceitual e um tutorial passo a passo sobre como usar Application Insights Profiler.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 02/23/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a300aa066bdef40c4768ac5e278537aec1a8b3b7
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820548"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Perfil ASP.NET Core aplicativos Web Linux do Azure com Application Insights Profiler

Esta funcionalidade encontra-se em pré-visualização.

Descubra quanto tempo é gasto em cada método de seu aplicativo Web em tempo real ao usar o [Application insights](../../azure-monitor/app/app-insights-overview.md). Application Insights Profiler agora está disponível para aplicativos Web ASP.NET Core hospedados no Linux no serviço Azure App. Este guia fornece instruções passo a passo sobre como os rastreamentos do profiler podem ser coletados para ASP.NET Core aplicativos Web do Linux.

Depois que você concluir este guia, o aplicativo poderá coletar rastreamentos do criador de perfil como os rastreamentos mostrados na imagem. Neste exemplo, o rastreamento do criador de perfil indica que uma solicitação da Web específica está lenta devido ao tempo gasto aguardando. O *caminho quente* no código que está atrasando o aplicativo é marcado por um ícone de chama. O método **about** na seção **HomeController** está retardando o aplicativo Web porque o método está chamando a função **thread. Sleep** .

![Rastreamentos do criador de perfil](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Pré-requisitos
As instruções a seguir se aplicam a todos os ambientes de desenvolvimento Windows, Linux e Mac:

* Instale o [SDK do .NET Core 2.1.2 ou posterior](https://dotnet.microsoft.com/download/archives).
* Instale o Git seguindo as instruções em [introdução-instalando o Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>Configurar o projeto localmente

1. Abra uma janela de prompt de comando em seu computador. As instruções a seguir funcionam para todos os ambientes de desenvolvimento Windows, Linux e Mac.

1. Criar um aplicativo Web ASP.NET Core MVC:

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

1. Altere o diretório de trabalho para a pasta raiz do projeto.

1. Adicione o pacote NuGet para coletar os rastreamentos do criador de perfil:

    ```shell
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

1. Habilitar Application Insights em Program.cs:

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseApplicationInsights() // Add this line of code to Enable Application Insights
            .UseStartup<Startup>();
    ```
    
1. Habilitar criador de perfil no Startup.cs:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddServiceProfiler(); // Add this line of code to Enable Profiler
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

1. Adicione uma linha de código na seção **HomeController.cs** para atrasar aleatoriamente alguns segundos:

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

1. Salve e confirme suas alterações no repositório local:

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>Criar o aplicativo Web do Linux para hospedar seu projeto

1. Criar o ambiente de aplicativo Web usando o serviço de aplicativo no Linux:

    ![Criar o aplicativo Web do Linux](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. Crie as credenciais de implantação:

    > [!NOTE]
    > Registre sua senha para usar posteriormente ao implantar seu aplicativo Web.

    ![Criar as credenciais de implantação](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Escolha as opções de implantação. Configure um repositório git local no aplicativo Web seguindo as instruções na portal do Azure. Um repositório Git é criado automaticamente.

    ![Configurar o repositório git](./media/profiler-aspnetcore-linux/setup-git-repo.png)

Para obter mais opções de implantação, consulte [Este artigo](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type).

## <a name="deploy-your-project"></a>Implantar seu projeto

1. Na janela do prompt de comando, navegue até a pasta raiz do seu projeto. Adicione um repositório remoto do git para apontar para o repositório no serviço de aplicativo:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Use o **nome de usuário** que você usou para criar as credenciais de implantação.
    * Use o **nome do aplicativo** que você usou para criar o aplicativo Web usando o serviço de aplicativo no Linux.

2. Implante o projeto enviando por push as alterações para o Azure:

    ```
    git push azure master
    ```

Você deverá ver uma saída semelhante ao exemplo a seguir:

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

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Adicionar Application Insights para monitorar seus aplicativos Web

1. [Crie um recurso de Application insights](./../../azure-monitor/app/create-new-resource.md ).

2. Copie o valor de **iKey** do recurso de Application insights e defina as seguintes configurações em seus aplicativos Web:

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ```

    Quando as configurações do aplicativo são alteradas, o site é reiniciado automaticamente. Depois que as novas configurações são aplicadas, o criador de perfil é executado imediatamente por dois minutos. O criador de perfil, em seguida, é executado por dois minutos a cada hora.

3. Gere algum tráfego para seu site. Você pode gerar tráfego atualizando a página **sobre** o site algumas vezes.

4. Aguarde de dois a cinco minutos para que os eventos sejam agregados a Application Insights.

5. Navegue até o painel de **desempenho** Application Insights na portal do Azure. Você pode exibir os rastreamentos do criador de perfil no canto inferior direito do painel.

    ![Exibir rastreamentos do criador de perfil](./media/profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>Problemas conhecidos

### <a name="profile-now-button-doesnt-work-for-linux-profiler"></a>O botão perfil agora não funciona para o perfil do Linux
A versão Linux do criador de perfil do App insights ainda não dá suporte à criação de perfil sob demanda usando o botão perfil agora.


## <a name="next-steps"></a>Passos seguintes
Se você usar contêineres personalizados hospedados pelo serviço Azure App, siga as instruções em [habilitar Service Profiler para um aplicativo ASP.NET Core em contêineres](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) para habilitar o Application insights Profiler.

Relate quaisquer problemas ou sugestões para o repositório Application Insights GitHub: [ApplicationInsights-Profiler-AspNetCore: issues](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).
