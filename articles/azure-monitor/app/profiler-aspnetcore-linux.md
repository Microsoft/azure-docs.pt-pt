---
title: Profile ASP.NET Core Azure Linux web apps com Perfilr de Insights de Aplicação / Microsoft Docs
description: Uma visão geral conceptual e um tutorial passo a passo sobre como usar o Perfil de Insights de Aplicação.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 02/23/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 9c98cd5d3d4d76f9455e4c036aa32a4ead20cfff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671720"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Profile ASP.NET aplicações web Core Azure Linux com Perfilde Insights de Aplicação

Esta funcionalidade encontra-se em pré-visualização.

Descubra quanto tempo é gasto em cada método da sua aplicação web ao utilizar Insights de [Aplicação](../../azure-monitor/app/app-insights-overview.md). Application Insights Profiler está agora disponível para ASP.NET aplicações web Core que estão hospedadas em Linux no Azure App Service. Este guia fornece instruções passo a passo sobre como os vestígios do Profiler podem ser recolhidos para ASP.NET aplicações web Do Core Linux.

Depois de completar esta passagem, a sua aplicação pode recolher vestígios de Profiler como os vestígios que são mostrados na imagem. Neste exemplo, o traço do Profiler indica que um pedido específico da web é lento devido ao tempo gasto à espera. O *caminho quente* no código que está a atrasar a aplicação é marcado por um ícone de chama. O método **Sobre** na secção **HomeController** está a atrasar a aplicação web porque o método está a chamar a função **Thread.Sleep.**

![Traços de perfil](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Pré-requisitos
As seguintes instruções aplicam-se a todos os ambientes de desenvolvimento do Windows, Linux e Mac:

* Instale o [.NET Core SDK 2.1.2 ou mais tarde](https://dotnet.microsoft.com/download/archives).
* Instale git seguindo as instruções de [Início - Instalação git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>Configurar o projeto localmente

1. Abra uma janela de comando rápida na sua máquina. As seguintes instruções funcionam para todos os ambientes de desenvolvimento windows, Linux e Mac.

1. Criar uma aplicação web ASP.NET Core MVC:

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

1. Mude o diretório de trabalho para a pasta raiz do projeto.

1. Adicione o pacote NuGet para recolher os vestígios do Profiler:

    ```shell
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

1. Ativar insights de aplicação em Program.cs:

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseApplicationInsights() // Add this line of code to Enable Application Insights
            .UseStartup<Startup>();
    ```
    
1. Ativar o Profiler em Startup.cs:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddServiceProfiler(); // Add this line of code to Enable Profiler
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

1. Adicione uma linha de código na secção **HomeController.cs** para atrasar aleatoriamente alguns segundos:

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

1. Poupe e comprometa as suas alterações no repositório local:

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>Crie a aplicação web Linux para acolher o seu projeto

1. Criar o ambiente de aplicações web utilizando o Serviço de Aplicações no Linux:

    ![Crie a aplicação web Linux](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. Criar as credenciais de implantação:

    > [!NOTE]
    > Grave a sua palavra-passe para usar mais tarde ao implementar a sua aplicação web.

    ![Criar as credenciais de implantação](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Escolha as opções de implementação. Crie um repositório git local na aplicação web seguindo as instruções no portal Azure. Um repositório Git é automaticamente criado.

    ![Configurar o repositório Git](./media/profiler-aspnetcore-linux/setup-git-repo.png)

Para mais opções de implementação, consulte [este artigo](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type).

## <a name="deploy-your-project"></a>Implementar o seu projeto

1. Na janela Command Prompt, navegue para a pasta raiz para o seu projeto. Adicione um repositório remoto Git para apontar para o repositório no Serviço de Aplicações:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Utilize o nome de **utilizador** que usou para criar as credenciais de implementação.
    * Utilize o nome da **aplicação** que usou para criar a aplicação web utilizando o Serviço de Aplicações no Linux.

2. Implemente o projeto empurrando as alterações para o Azure:

    ```
    git push azure master
    ```

Deve ver a saída semelhante ao seguinte exemplo:

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

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Adicione Insights de Aplicação para monitorizar as suas aplicações web

1. [Criar um recurso Deinsights de Aplicação.](./../../azure-monitor/app/create-new-resource.md )

2. Copie o valor **iKey** do recurso Application Insights e delineie as seguintes definições nas suas aplicações web:

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ```

    Quando as definições da aplicação são alteradas, o site reinicia automaticamente. Após a aplicação das novas definições, o Profiler funciona imediatamente durante dois minutos. O Profiler funciona por dois minutos a cada hora.

3. Gere algum tráfego para o seu site. Pode gerar tráfego refrescando o site **Sobre** a página algumas vezes.

4. Aguarde dois a cinco minutos para que os eventos agregem-se à Application Insights.

5. Navegue no painel de **desempenho** de Insights de Aplicação no portal Azure. Pode ver os vestígios do Profiler na parte inferior direita do painel.

    ![Ver vestígios de perfil](./media/profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>Problemas conhecidos

### <a name="profile-now-button-doesnt-work-for-linux-profiler"></a>O botão Profile Now não funciona para o Perfil Linux
A versão Linux do perfil app Insights ainda não suporta o perfil da procura utilizando o botão agora de perfil.


## <a name="next-steps"></a>Passos seguintes
Se utilizar recipientes personalizados hospedados pelo Serviço de Aplicações Azure, siga as instruções no ['Enable Service Profiler' para uma aplicação ASP.NET Core contentorizada](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) para ativar o Perfil de Insights de Aplicação.

Reporte quaisquer problemas ou sugestões ao repositório DeInsights gitHub da aplicação: [ApplicationInsights-Profiler-AspNetCore: Issues](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).
