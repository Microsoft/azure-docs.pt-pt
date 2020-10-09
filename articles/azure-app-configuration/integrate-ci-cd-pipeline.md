---
title: Integre a Configuração de Aplicativos Azure utilizando um pipeline de integração e entrega contínua
description: Aprenda a implementar integração e entrega contínua usando a Configuração de Aplicações Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.date: 01/30/2020
ms.author: lcozzens
ms.openlocfilehash: f0d1e57c7e212fefc6e17a8170e3b4537b190f60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88211650"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrar num pipeline CI/CD

Este artigo explica como utilizar dados da Azure App Configuration num sistema de integração contínua e implementação contínua.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Utilize a configuração da aplicação no seu pipeline Azure DevOps

Se tiver um Pipeline Azure DevOps, pode obter valores-chave da Configuração da Aplicação e defini-los como variáveis de tarefa. A [extensão de DevOps de Configuração de Aplicação Azure](https://go.microsoft.com/fwlink/?linkid=2091063) é um módulo adicional que fornece esta funcionalidade. Siga as instruções para utilizar a extensão numa sequência de tarefas de construção ou libertação.

## <a name="deploy-app-configuration-data-with-your-application"></a>Implementar dados de configuração de aplicativos com a sua aplicação

A sua aplicação pode não funcionar se depender da Configuração da Aplicação Azure e não conseguir alcançá-la. Melhore a resiliência da sua aplicação através de dados de configuração de embalagem num ficheiro que é implantado com a aplicação e carregado localmente durante o arranque da aplicação. Esta abordagem garante que a sua aplicação tem valores de definição padrão no arranque. Estes valores são substituídos por quaisquer alterações mais recentes numa loja de Configuração de Aplicações quando estiver disponível.

Utilizando a função [Exportação](./howto-import-export-data.md#export-data) da Configuração da Aplicação Azure, pode automatizar o processo de recuperação dos dados de configuração atuais como um único ficheiro. Em seguida, pode incorporar este ficheiro num passo de construção ou implantação no seu pipeline de integração contínua e implementação contínua (CI/CD).

O exemplo a seguir mostra como incluir os dados de Configuração de Aplicações como um passo de construção para a aplicação web introduzida nos quickstarts. Antes de continuar, termine [Criar uma aplicação Core ASP.NET com a Configuração de Aplicações](./quickstart-aspnet-core-app.md) em primeiro lugar.

Você pode usar qualquer editor de código para fazer os passos neste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível nas plataformas Windows, macOS e Linux.

### <a name="prerequisites"></a>Pré-requisitos

Se você construir localmente, faça o download e instale o [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se ainda não o fez.

Para fazer uma construção em nuvem, com Azure DevOps, por exemplo, certifique-se de que o [CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) está instalado no seu sistema de construção.

### <a name="export-an-app-configuration-store"></a>Exportar uma loja de configuração de aplicativos

1. Abra o seu ficheiro *.csproj* e adicione o seguinte script:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```
1. Abra *Program.cs*, e atualize o `CreateWebHostBuilder` método para utilizar o ficheiro JSON exportado, chamando o `config.AddJsonFile()` método.  Adicione o `System.Reflection` espaço de nome também.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

### <a name="build-and-run-the-app-locally"></a>Construa e execute a app localmente

1. Desaprote uma variável ambiental chamada **ConnectionString**e desaprote-a na chave de acesso à sua loja de Configuração de Aplicações. 
    Se utilizar o pedido de comando do Windows, executar o seguinte comando e reiniciar a solicitação de comando para permitir que a alteração entre em vigor:

    ```console
     setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o Windows PowerShell, executar o seguinte comando:

    ```powershell
     $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o macOS ou o Linux, executar o seguinte comando:

    ```console
     export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Para construir a aplicação utilizando o .NET Core CLI, executar o seguinte comando na concha de comando:

    ```console
     dotnet build
    ```

3. Após a construção concluída com sucesso, executar o seguinte comando para executar a aplicação web localmente:

    ```console
     dotnet run
    ```

4. Abra uma janela do navegador e vá para `http://localhost:5000` , que é o URL padrão para a aplicação web hospedada localmente.

    ![Quickstart app lançar local](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, exportaste dados de Configuração de Aplicações Azure para serem utilizados num oleoduto de implantação. Para saber mais sobre como usar a Configuração de Aplicações, continue para as amostras do Azure CLI.

> [!div class="nextstepaction"]
> [CLI do Azure](https://docs.microsoft.com/cli/azure/appconfig?view=azure-cli-latest)
