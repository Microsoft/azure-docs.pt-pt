---
title: Quickstart para Configuração de AplicativoS Azure com ASP.NET Core [ Microsoft Docs
description: Quickstart para usar configuração de aplicativos Azure com aplicações ASP.NET Core
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 2dc2143619594c8fd46fa4e838b97a3ecde95653
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027720"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Quickstart: Criar uma aplicação Core ASP.NET com configuração de aplicações Azure

Neste arranque rápido, utilizará a Configuração de Aplicações Azure para centralizar o armazenamento e gestão das definições de aplicações para uma aplicação Core ASP.NET. ASP.NET Core constrói um único objeto de configuração baseado em valor-chave utilizando definições de uma ou mais fontes de dados especificadas por uma aplicação. Estas fontes de dados são conhecidas como *fornecedores de configuração.* Como o cliente .NET Core da Configuração de Aplicações é implementado como um fornecedor de configuração, o serviço aparece como outra fonte de dados.

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [SDK .NET Core](https://dotnet.microsoft.com/download)

>[!TIP]
> O Azure Cloud Shell é uma concha interativa gratuita que pode usar para executar as instruções da linha de comando neste artigo.  Tem ferramentas Azure comuns pré-instaladas, incluindo o .NET Core SDK. Se tiver iniciado sessão na sua subscrição Azure, lance a sua [Azure Cloud Shell](https://shell.azure.com) a partir de shell.azure.com.  Você pode saber mais sobre Azure Cloud Shell [lendo a nossa documentação](../cloud-shell/overview.md)

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Configuration Explorer**  >  **Crie**  >  **valor-chave** para adicionar os seguintes pares de valor-chave:

    | Chave | Valor |
    |---|---|
    | TestApp:Definições:BackgroundColor | Branco |
    | TestApp:Definições:FontSize | 24 |
    | TestApp:Definições:FontColor | Preto |
    | TestApp:Definições:Mensagem | Dados da Configuração da Aplicação Azure |

    Deixe **a etiqueta** e o tipo de **conteúdo** vazios por enquanto. Selecione **Aplicar**.

## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

Utilize a [interface de linha de comando .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) para criar um novo projeto de aplicação web Core MVC ASP.NET. O [Azure Cloud Shell](https://shell.azure.com) fornece estas ferramentas para si.  Também estão disponíveis nas plataformas Windows, macOS e Linux.

1. Crie uma pasta nova para o projeto. Para este arranque rápido, nomeie-o *TestAppConfig*.

1. Na nova pasta, executar o seguinte comando para criar um novo projeto de aplicação web Core MVC ASP.NET:

```dotnetcli
dotnet new mvc --no-https
```

## <a name="add-secret-manager"></a>Adicionar Gerente Secreto

Para utilizar o Gestor Secreto, adicione um `UserSecretsId` elemento ao seu ficheiro *.csproj.*

1. Abra o ficheiro *.csproj.*

1.  Adicione um `UserSecretsId` elemento como mostrado aqui. Pode usar o mesmo GUID, ou pode substituir este valor pelo seu.

    > [!IMPORTANT]
    > `CreateHostBuilder`substitui `CreateWebHostBuilder` em .NET Core 3.0.  Selecione a sintaxe correta com base no seu ambiente.
    
    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    
    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    
        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>
    
    </Project>
    ```
    
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    
    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
        
        <PropertyGroup>
            <TargetFramework>netcoreapp3.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
    </Project>
    ```
    ---

1. Guarde o ficheiro *.csproj.*

Esta ferramenta armazena os dados confidenciais dos projetos de programação fora da árvore dos projetos. Esta abordagem ajuda a evitar a partilha acidental de segredos de aplicações no código fonte.

> [!TIP]
> Para saber mais sobre o Secret Manager, consulte [o armazenamento seguro de segredos de aplicações em desenvolvimento em ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/app-secrets)

## <a name="connect-to-an-app-configuration-store"></a>Conecte-se a uma loja de configuração de aplicativos

1. Adicione uma referência ao `Microsoft.Azure.AppConfiguration.AspNetCore` pacote NuGet executando o seguinte comando:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Executar o seguinte comando para restaurar pacotes para o seu projeto:

    ```dotnetcli
    dotnet restore
    ```

1. Adicione um segredo chamado *ConnectionStrings:AppConfig* ao Gestor Secreto.

    Este segredo contém o fio de ligação para aceder à sua loja de Configuração de Aplicações. Substitua o valor no seguinte comando com o fio de ligação para a sua loja de Configuração de Aplicações. Pode encontrar a cadeia de ligação sob **as teclas de acesso** no portal Azure.

    Este comando tem de ser executado no mesmo diretório que o ficheiro *.csproj*.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Algumas conchas truncam a cadeia de ligação a menos que esteja fechada em aspas. Certifique-se de que a saída do `dotnet user-secrets` comando mostra toda a cadeia de ligação. Se não o fizer, reexame o comando, encerrando a cadeia de ligação em aspas.

    O Secret Manager é usado apenas para testar a aplicação web localmente. Quando a aplicação é implantada no [Azure App Service](https://azure.microsoft.com/services/app-service/web), por exemplo, utiliza a definição de aplicação **De Cordas de Ligação** no Serviço de Aplicações em vez de Secret Manager para armazenar a cadeia de ligação.

    Aceda a este segredo usando a configuração API. Um cólon (:) funciona no nome de configuração com a configuração API em todas as plataformas suportadas. Ver [Configuração por ambiente](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

1. Abra *Program.cs*, e adicione uma referência ao fornecedor de Configuração de Aplicação .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Atualize o `CreateWebHostBuilder` método para utilizar a Configuração da Aplicação chamando o `config.AddAzureAppConfiguration()` método.

    > [!IMPORTANT]
    > `CreateHostBuilder`substitui `CreateWebHostBuilder` em .NET Core 3.0.  Selecione a sintaxe correta com base no seu ambiente.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
        })
        .UseStartup<Startup>());
    ```

    ---

1. Navegue para * <app root> /Vistas/Casa* e abra *Index.cshtml*. Substitua o seu conteúdo pelo seguinte código:

    ```HTML
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]</h1>
    ```

1. Navegue para * <app root> /Views/Shared* and open *_Layout.cshtml*. Substitua o seu conteúdo pelo seguinte código:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - hello_world</title>

        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </head>
    <body>
        <div class="container body-content">
            @RenderBody()
        </div>

        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>

        @RenderSection("Scripts", required: false)
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Construa e execute a app localmente

1. Para construir a aplicação utilizando o .NET Core CLI, navegue para o diretório de raiz da sua aplicação e execute o seguinte comando na concha de comando:

    ```dotnetcli
    dotnet build
    ```

1. Após a construção concluída com sucesso, executar o seguinte comando para executar a aplicação web localmente:

    ```dotnetcli
    dotnet run
    ```

1. Se estiver a trabalhar na sua máquina local, utilize um browser para navegar `http://localhost:5000` para . Este é o URL padrão para a aplicação web hospedada localmente.  

Se estiver a trabalhar no Azure Cloud Shell, selecione o botão *de pré-visualização web* seguido de *Configuração*.  

![Localizar o botão de pré-visualização web](./media/quickstarts/cloud-shell-web-preview.png)

Quando solicitado para configurar a porta para pré-visualização, insira '5000' e *selecione Abrir e navegar*.  A página web irá ler "Data from Azure App Configuration".

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou uma nova loja de Configuração de Aplicações e utilizou-a com uma aplicação web Core ASP.NET através do [fornecedor de Configuração de Aplicações.](https://go.microsoft.com/fwlink/?linkid=2074664) Para aprender a configurar a sua ASP.NET aplicação Core para atualizar dinamicamente as definições de configuração, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Ativar a configuração dinâmica](./enable-dynamic-configuration-aspnet-core.md)
