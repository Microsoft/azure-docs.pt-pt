---
title: Quickstart para configuração de aplicativos Azure com ASP.NET core | Microsoft Docs
description: Crie uma aplicação Core ASP.NET com configuração de aplicação Azure para centralizar o armazenamento e gestão das definições de aplicações para uma aplicação core ASP.NET.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp, contperf-fy21q1
ms.topic: quickstart
ms.date: 09/25/2020
ms.author: alkemper
ms.openlocfilehash: 353a934842fd872d48d091f1df1b6d94ac4599ff
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663439"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Quickstart: Criar uma aplicação Core ASP.NET com configuração de aplicações Azure

Neste arranque rápido, utilizará a Configuração de Aplicações Azure para centralizar o armazenamento e gestão das definições de aplicações para uma aplicação core ASP.NET. ASP.NET Core constrói um único objeto de configuração baseado em valor-chave usando definições de uma ou mais fontes de dados especificadas por uma aplicação. Estas fontes de dados são conhecidas como *fornecedores de configuração.* Como o cliente .NET Core da Configuração de Aplicações é implementado como um fornecedor de configuração, o serviço aparece como outra fonte de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/dotnet)
* [SDK .NET Core](https://dotnet.microsoft.com/download)

> [!TIP]
> O Azure Cloud Shell é uma concha interativa e livre que pode usar para executar as instruções da linha de comando neste artigo. Tem ferramentas Azure comuns pré-instaladas, incluindo o .NET Core SDK. Se iniciar sessão na sua subscrição Azure, lance a sua [Azure Cloud Shell](https://shell.azure.com) a partir de shell.azure.com. Você pode saber mais sobre Azure Cloud Shell [lendo a nossa documentação](../cloud-shell/overview.md)

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicativos

[!INCLUDE[Azure App Configuration resource creation steps](../../includes/azure-app-configuration-create.md)]

7. Selecione Explorador de configuração de **configuração de operações**  >    >  **Crie**  >  **valor chave** para adicionar os seguintes pares de valor-chave:

    | Chave                                | Valor                               |
    |------------------------------------|-------------------------------------|
    | `TestApp:Settings:BackgroundColor` | *#FFF*                              |
    | `TestApp:Settings:FontColor`       | *#000*                              |
    | `TestApp:Settings:FontSize`        | *24*                                |
    | `TestApp:Settings:Message`         | *Dados da Configuração da Aplicação Azure* |

    Deixe **o rótulo** e o **conteúdo** vazios por enquanto. Selecione **Aplicar**.

## <a name="create-an-aspnet-core-web-app"></a>Criar uma aplicação Web ASP.NET Core

Utilize a [interface de linha de comando .NET Core (CLI)](/dotnet/core/tools) para criar um novo projeto core MVC ASP.NET. O [Azure Cloud Shell](https://shell.azure.com) fornece estas ferramentas para si. Também estão disponíveis nas plataformas Windows, macOS e Linux.

Executar o seguinte comando para criar um projeto core MVC ASP.NET numa nova pasta *TestAppConfig:*

```dotnetcli
dotnet new mvc --no-https --output TestAppConfig
```

[!INCLUDE[Add Secret Manager support to an ASP.NET Core project](../../includes/azure-app-configuration-add-secret-manager.md)]

## <a name="connect-to-the-app-configuration-store"></a>Conecte-se à loja de Configuração de Aplicações

1. Executar o seguinte comando para adicionar uma referência de pacote [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) NuGet:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Executar o seguinte comando no mesmo diretório que o ficheiro *.csproj.* O comando utiliza o Secret Manager para armazenar um segredo chamado `ConnectionStrings:AppConfig` , que armazena a cadeia de conexão para a sua loja de Configuração de Aplicações. Substitua o `<your_connection_string>` espaço reservado pela cadeia de ligação da sua loja de configuração de aplicações. Pode encontrar a cadeia de ligação sob **as teclas de acesso** no portal Azure.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```

    > [!IMPORTANT]
    > Algumas conchas truncam a corda de ligação a menos que esteja fechada em aspas. Certifique-se de que a saída do `dotnet user-secrets list` comando mostra toda a cadeia de ligação. Se não o fizer, reexame o comando, encerrando a cadeia de ligação em aspas.

    O Secret Manager é usado apenas para testar a aplicação web localmente. Quando a aplicação for implantada no [Azure App Service,](https://azure.microsoft.com/services/app-service/web)utilize a definição da aplicação **Connection Strings** no Serviço de Aplicações em vez de Secret Manager para armazenar a cadeia de ligação.

    Aceda a este segredo utilizando a API de Configuração de Núcleo .NET. Um cólon `:` () funciona no nome de configuração com a API de configuração em todas as plataformas suportadas. Para obter mais informações, consulte [as teclas e valores de Configuração](/aspnet/core/fundamentals/configuration#configuration-keys-and-values).

1. Em *Program.cs*, adicione uma referência ao espaço de nomes API de configuração de .NET:

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

1. Atualize o `CreateWebHostBuilder` método para utilizar a Configuração da Aplicação chamando o `AddAzureAppConfiguration` método.

    > [!IMPORTANT]
    > `CreateHostBuilder` substitui `CreateWebHostBuilder` em .NET Core 3.x. Selecione a sintaxe correta com base no seu ambiente.

     #### <a name="net-5x"></a>[.NET 5.x](#tab/core5x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(connection);
                }).UseStartup<Startup>());
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(connection);
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                var connection = settings.GetConnectionString("AppConfig");
                config.AddAzureAppConfiguration(connection);
            })
            .UseStartup<Startup>();
    ```

    ---

    Com a alteração anterior, o [fornecedor de configuração para configuração de aplicações](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration) foi registado na API de Configuração de Núcleo .NET.

## <a name="read-from-the-app-configuration-store"></a>Leia na loja de Configuração de Aplicações

Preencha os seguintes passos para ler e exibir valores armazenados na loja de Configuração de Aplicações. A API de Configuração de Núcleo .NET será utilizada para aceder à loja. A sintaxe da navalha será utilizada para mostrar os valores das teclas.

Abra *\<app root> /Vistas/Home/Index.cshtml,* e substitua o seu conteúdo pelo seguinte código:

```cshtml
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

No código anterior, as chaves da loja de configuração de aplicações são utilizadas da seguinte forma:

* O `TestApp:Settings:BackgroundColor` valor da chave é atribuído à propriedade CSS. `background-color`
* O `TestApp:Settings:FontColor` valor da chave é atribuído à propriedade CSS. `color`
* O `TestApp:Settings:FontSize` valor da chave é atribuído à propriedade CSS. `font-size`
* O `TestApp:Settings:Message` valor da chave é apresentado como um título.

## <a name="build-and-run-the-app-locally"></a>Construa e execute a app localmente

1. Para construir a aplicação utilizando o .NET Core CLI, navegue para o diretório de raiz do seu projeto. Execute o seguinte comando na shell de comandos:

    ```dotnetcli
    dotnet build
    ```

1. Depois de concluída a construção com sucesso, executar o seguinte comando para executar a aplicação web localmente:

    ```dotnetcli
    dotnet run
    ```

1. Se estiver a trabalhar na sua máquina local, utilize um browser para navegar `http://localhost:5000` para . Este endereço é o URL padrão para a aplicação web hospedada localmente. Se estiver a trabalhar no Azure Cloud Shell, selecione o botão **de pré-visualização web** seguido de **Configuração**.

    ![Localizar o botão de pré-visualização web](./media/quickstarts/cloud-shell-web-preview.png)

    Quando solicitado para configurar a porta para pré-visualização, introduza *5000* e **selecione Abrir e navegar**. A página web irá ler "Data from Azure App Configuration".

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, irá:

* Aprovisionou uma nova loja de Configuração de Aplicações.
* Registou o fornecedor de configuração .NET Core da loja de configuração de aplicações.
* Leia as chaves da loja de configuração da aplicação com o fornecedor de configuração.
* A loja de configuração de aplicações apresentou os valores-chave da loja de configuração de aplicações utilizando a sintaxe razor.

Para aprender a configurar a sua ASP.NET aplicação Core para atualizar dinamicamente as definições de configuração, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Ativar a configuração dinâmica](./enable-dynamic-configuration-aspnet-core.md)