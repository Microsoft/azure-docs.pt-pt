---
title: Perfil ASP.NET aplicações web Core Azure Linux com perfil de insights de aplicação | Microsoft Docs
description: Uma visão geral conceptual e um tutorial passo a passo sobre como usar o Profiler Application Insights.
ms.topic: conceptual
ms.custom: devx-track-csharp
author: cweining
ms.author: cweining
ms.date: 02/23/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 4c208a80a1f701cc3e0c1c5cd08a999f2f15815e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889081"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Perfil ASP.NET aplicações web Core Azure Linux com perfil de insights de aplicação

Esta funcionalidade encontra-se em pré-visualização.

Descubra quanto tempo é gasto em cada método da sua aplicação web ao vivo ao utilizar [o Application Insights](./app-insights-overview.md). O Application Insights Profiler já se encontra disponível para aplicações web core ASP.NET que estão hospedadas no Linux no Azure App Service. Este guia fornece instruções passo a passo sobre como os traços do Profiler podem ser recolhidos para ASP.NET aplicações web Core Linux.

Depois de completar esta passagem, a sua aplicação pode recolher vestígios de Profiler como os vestígios que são mostrados na imagem. Neste exemplo, o traço profiler indica que um determinado pedido web é lento devido ao tempo gasto à espera. O *caminho quente* no código que está a atrasar a aplicação é marcado por um ícone de chama. O método **Sobre** a secção **"HomeController"** está a atrasar a aplicação web porque o método chama a função **Thread.Sleep.**

![Traços de perfis](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Pré-requisitos
As seguintes instruções aplicam-se a todos os ambientes de desenvolvimento de Windows, Linux e Mac:

* Instale o [.NET Core SDK 2.1.2 ou posteriormente](https://dotnet.microsoft.com/download/archives).
* Instale o Git seguindo as instruções em ["Iniciar" - Instalar o Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>Configurar o projeto localmente

1. Abra uma janela de aviso de comando na sua máquina. As seguintes instruções funcionam para todos os ambientes de desenvolvimento de Windows, Linux e Mac.

1. Crie uma aplicação web core MVC ASP.NET:

   ```console
   dotnet new mvc -n LinuxProfilerTest
   ```

1. Mude o diretório de trabalho para a pasta raiz do projeto.

1. Adicione o pacote NuGet para recolher os vestígios do Profiler:

   ```console
   dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
   ```

1. Ativar insights de aplicações e profiler em startup.cs:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry(); // Add this line of code to enable Application Insights.
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

1. Guarde e comprometa as suas alterações ao repositório local:

    ```console
    git init
    git add .
    git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>Crie a aplicação web Linux para hospedar o seu projeto

1. Crie o ambiente de aplicações web utilizando o Serviço de Aplicações no Linux:

    ![Crie a aplicação web Linux](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. Criar as credenciais de implantação:

    > [!NOTE]
    > Grave a sua palavra-passe para usar mais tarde ao implementar a sua aplicação web.

    ![Criar as credenciais de implantação](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Escolha as opções de implementação. Crie um repositório git local na aplicação web seguindo as instruções no portal Azure. Um repositório de Git é criado automaticamente.

    ![Configurar o repositório git](./media/profiler-aspnetcore-linux/setup-git-repo.png)

Para obter mais opções de implementação, consulte [a documentação do Serviço de Aplicações.](../../app-service/index.yml)

## <a name="deploy-your-project"></a>Implemente o seu projeto

1. Na janela 'Pedido de Comando' da sua janela De comando, navegue na pasta raiz para o seu projeto. Adicione um repositório remoto Git para apontar para o repositório no Serviço de Aplicações:

    ```console
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Utilize o **nome de utilizador** utilizado para criar as credenciais de implantação.
    * Utilize o nome da **aplicação** que usou para criar a aplicação web utilizando o Serviço de Aplicações no Linux.

2. Implementar o projeto empurrando as alterações para a Azure:

    ```console
    git push azure main
    ```

    Deve ver a saída semelhante ao seguinte exemplo:

    ```output
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'main'.
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
    ...
    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Adicionar Insights de Aplicação para monitorizar as suas aplicações web

1. [Criar um recurso Application Insights](./create-new-resource.md).

2. Copie o valor **iKey** do recurso Application Insights e defina as seguintes definições nas suas aplicações web:

    `APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]`

    Quando as definições da aplicação são alteradas, o site reinicia automaticamente. Após a aplicação das novas definições, o Profiler funciona imediatamente durante dois minutos. Em seguida, o Profiler funciona durante dois minutos a cada hora.

3. Gere algum tráfego para o seu website. Pode gerar tráfego refrescando o site **Sobre** página algumas vezes.

4. Aguarde dois a cinco minutos para que os eventos se ausquem à Application Insights.

5. Navegue no painel de **desempenho** do Application Insights no portal Azure. Pode ver os traços do Profiler na parte inferior direita do painel.

    ![Ver traços do profiler](./media/profiler-aspnetcore-linux/view-traces.png)



## <a name="next-steps"></a>Passos seguintes
Se utilizar recipientes personalizados que são hospedados pelo Azure App Service, siga as instruções no [ Enable Service Profiler para uma aplicação core de ASP.NET contentorizada](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) para ativar o Perfil de Insights de Aplicação.

Reportar quaisquer problemas ou sugestões ao repositório de Insights de Aplicação GitHub: [ApplicationInsights-Profiler-AspNetCore: Problemas](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).
