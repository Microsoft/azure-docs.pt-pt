---
title: Tutorial para a integração com um pipeline de integração e entrega contínua usando a configuração do Azure App | Microsoft Docs
description: Neste tutorial, você aprenderá a gerar um arquivo de configuração usando dados na configuração Azure App durante a integração e a entrega contínuas
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: e9b81baed14b18c6db736bd94a2aba43a4e671ad
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185111"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrar num pipeline CI/CD

Este artigo descreve várias maneiras de usar dados da configuração Azure App em uma integração contínua e um sistema de implantação contínua.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Usar a configuração de aplicativo em seu pipeline DevOps do Azure

Se você tiver um pipeline DevOps do Azure, poderá buscar valores de chave da configuração do aplicativo e defini-los como variáveis de tarefa. O [Azure app extensão DevOps de configuração](https://go.microsoft.com/fwlink/?linkid=2091063) é um módulo complementar que fornece essa funcionalidade. Basta seguir suas instruções para usar a extensão em uma sequência de tarefas de compilação ou versão.

## <a name="deploy-app-configuration-data-with-your-application"></a>Implantar dados de configuração de aplicativo com seu aplicativo

Seu aplicativo poderá falhar ao ser executado se depender de Azure App configuração e não puder acessá-lo. Você pode aprimorar a resiliência do seu aplicativo para lidar com esse evento, mas improvável que isso aconteça. Para fazer isso, empacote os dados de configuração atuais em um arquivo que é implantado com o aplicativo e carregado localmente durante sua inicialização. Essa abordagem garante que seu aplicativo tenha valores de configuração padrão pelo menos. Esses valores são substituídos por quaisquer alterações mais recentes em um repositório de configuração de aplicativo quando ele está disponível.

Usando a função [Exportar](./howto-import-export-data.md#export-data) da configuração do Azure app, você pode automatizar o processo de recuperação de dados de configuração atuais como um único arquivo. Em seguida, incorpore esse arquivo em uma etapa de compilação ou implantação em seu pipeline de integração contínua e implantação contínua (CI/CD).

O exemplo a seguir mostra como incluir dados de configuração de aplicativo como uma etapa de compilação para o aplicativo Web introduzido nos guias de início rápido. Antes de continuar, conclua a [criação de um aplicativo ASP.NET Core com a configuração de aplicativo](./quickstart-aspnet-core-app.md) primeiro.

Você pode usar qualquer editor de código para executar as etapas neste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível nas plataformas Windows, MacOS e Linux.

### <a name="prerequisites"></a>Pré-requisitos

Se você criar localmente, baixe e instale o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se ainda não tiver feito isso.

Para fazer uma compilação em nuvem, com o Azure DevOps, por exemplo, verifique se o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) está instalado em seu sistema de compilação.

### <a name="export-an-app-configuration-store"></a>Exportar um repositório de configurações de aplicativo

1. Abra o arquivo *. csproj* e adicione o seguinte script:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Adicione o *ConnectionString* associado ao repositório de configuração do aplicativo como uma variável de ambiente.

2. Abra *Program.cs*e atualize o método `CreateWebHostBuilder` para usar o arquivo JSON exportado chamando o método `config.AddJsonFile()`.

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

### <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Defina uma variável de ambiente chamada **ConnectionString**e defina-a como a chave de acesso para seu repositório de configuração de aplicativo. Se você usar o prompt de comando do Windows, execute o seguinte comando e reinicie o prompt de comando para permitir que a alteração entre em vigor:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se você usar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se você usar o macOS ou Linux, execute o seguinte comando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Para compilar o aplicativo usando o CLI do .NET Core, execute o seguinte comando no Shell de comando:

        dotnet build

3. Depois que a compilação for concluída com êxito, execute o seguinte comando para executar o aplicativo Web localmente:

        dotnet run

4. Abra uma janela do navegador e vá para `http://localhost:5000`, que é a URL padrão para o aplicativo Web hospedado localmente.

    ![Local de inicialização do aplicativo de início rápido](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você exportou Azure App dados de configuração a serem usados em um pipeline de implantação. Para saber mais sobre como usar a configuração de aplicativo, prossiga para os exemplos de CLI do Azure.

> [!div class="nextstepaction"]
> [Integração de identidade gerenciada](./howto-integrate-azure-managed-service-identity.md)
