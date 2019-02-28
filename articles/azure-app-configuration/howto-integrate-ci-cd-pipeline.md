---
title: Integrar com um pipeline de integração e entrega contínuo com a configuração de aplicações do Azure | Documentos da Microsoft
description: Saiba como gerar um ficheiro de configuração a utilizar os dados na configuração de aplicações do Azure durante a integração e entrega contínuas
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 7b2e919bc46810e8478956675ffeb1cb0542da85
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957373"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrar com um pipeline CI/CD

Para melhorar a resiliência da sua aplicação contra a remota possibilidade de não ser capaz de alcançar a configuração de aplicações do Azure, deve empacotar os dados de configuração atual num arquivo que é implementado com a aplicação e carregá-lo localmente durante o arranque do seu . Essa abordagem garante que seu aplicativo terão valores de definição do padrão pelo menos. Estes valores serão substituídos por quaisquer alterações mais recentes feitas num arquivo de configuração de aplicação quando estiver disponível.

Utilizar o [ **exportar** ](./howto-import-export-data.md#export-data) função de configuração de aplicações do Azure, pode automatizar o processo de obtenção de dados de configuração atual como um único arquivo. Em seguida, pode incorporar esse arquivo numa etapa de compilação ou implementação na sua integração contínua e um pipeline de implementação contínua.

O exemplo seguinte mostra como incluir a configuração de aplicações passo de dados como uma compilação para a aplicação web, introduzida nos inícios rápidos. Concluída [criar uma aplicação ASP.NET Core com a configuração de aplicações](./quickstart-aspnet-core-app.md) primeiro antes de continuar.

Pode utilizar qualquer editor de código para concluir os passos deste início rápido. No entanto, o [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção, disponível nas plataformas Windows, macOS e Linux.

## <a name="prerequisites"></a>Pré-requisitos

Se criar localmente, transfira e instale [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se ainda não o fez.

Se quiser fazer uma compilação de cloud, com o Azure DevOps, por exemplo, certifique-se [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) está instalado no seu sistema de compilação.

## <a name="export-app-configuration-store"></a>Exportar o arquivo de configuração de aplicação

1. Abra sua *. csproj* de ficheiros e adicione o seguinte:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    O *ConnectionString* associados com a sua configuração de aplicação loja deve ser adicionada como uma variável de ambiente.

2. Open *Program.cs* e atualizar a `CreateWebHostBuilder` método a utilizar o ficheiro exportado json ao chamar o `config.AddJsonFile()` método.

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

## <a name="build-and-run-the-app-locally"></a>Criar e executar a aplicação localmente

1. Definir uma variável de ambiente com o nome **ConnectionString** e defini-lo para a chave de acesso ao seu arquivo de configuração de aplicação. Se estiver a utilizar o Prompt de comando do Windows, execute o seguinte comando e reinicie o Prompt de comando para permitir que a alteração tenha efeito:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se estiver a utilizar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se estiver a utilizar o macOS ou Linux, execute o seguinte comando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Para criar a aplicação com a CLI de .NET Core, execute o seguinte comando na shell de comandos:

        dotnet build

3. Depois de concluir com êxito a compilação, execute o seguinte comando para executar a aplicação Web localmente:

        dotnet run

4. Inicie uma janela do browser e navegue para `http://localhost:5000`, que é o URL predefinido da aplicação web alojada localmente.

    ![Início rápido iniciação da aplicação local](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Passos Seguintes

* [Identidades geridas para a integração de recursos do Azure](./integrate-azure-managed-service-identity.md)
