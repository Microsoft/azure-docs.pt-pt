---
title: Tutorial para a integração com um pipeline de integração e entrega contínuo ao utilizar a configuração de aplicações do Azure | Documentos da Microsoft
description: Neste tutorial, irá aprender a gerar um ficheiro de configuração, utilizando dados de configuração de aplicação do Azure durante a integração e entrega contínuas
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
ms.openlocfilehash: 26bd49af7245d6e6dde3162a2e1d95c54f13e35b
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415953"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrar num pipeline CI/CD

Este artigo descreve várias formas de utilizar dados de configuração de aplicações do Azure num sistema de implementação contínua e a integração contínua.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Utilizar configuração de aplicações no seu Pipeline de DevOps do Azure

Se tiver um Pipeline de DevOps do Azure, pode obter valores de chave de configuração de aplicações e defini-las como variáveis de tarefas. O [extensão de DevOps de configuração de aplicações do Azure](https://go.microsoft.com/fwlink/?linkid=2091063) é um módulo de suplemento que fornece essa funcionalidade. Basta seguir suas instruções para usar a extensão de uma compilação ou a sequência de tarefas de lançamento.

## <a name="deploy-app-configuration-data-with-your-application"></a>Implementar dados de configuração de aplicação com a sua aplicação

Seu aplicativo poderá não conseguir executar se ela depende da configuração de aplicações do Azure e não é possível aceder ao mesmo. Pode melhorar a resiliência do seu aplicativo para lidar com o caso, no entanto improvável é acontecer. Para fazer isso, o pacote para um ficheiro que é implementado com a aplicação e carregá-lo localmente durante o arranque do seu os dados de configuração atual. Essa abordagem garante que seu aplicativo tem, pelo menos, os valores de definição de predefinição. Estes valores são substituídos por quaisquer alterações mais recentes feitas num arquivo de configuração de aplicação quando estiver disponível.

Utilizar o [exportar](./howto-import-export-data.md#export-data) função de configuração de aplicações do Azure, pode automatizar o processo de obtenção de dados de configuração atual como um único arquivo. Em seguida, incorpore esse arquivo numa etapa de compilação ou implementação na sua integração contínua e um pipeline de implementação contínua (CI/CD).

O exemplo seguinte mostra como incluir a configuração de aplicações passo de dados como uma compilação para a aplicação web, introduzida nos inícios rápidos. Antes de continuar, concluir [criar uma aplicação ASP.NET Core com a configuração de aplicações](./quickstart-aspnet-core-app.md) primeiro.

Pode utilizar qualquer editor de código para realizar os passos neste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível no Windows, macOS e plataformas Linux.

### <a name="prerequisites"></a>Pré-requisitos

Se criar localmente, transferir e instalar o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se ainda não o fez.

Para fazer uma compilação de cloud, com o Azure DevOps, por exemplo, certifique-se a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) está instalado no seu sistema de compilação.

### <a name="export-an-app-configuration-store"></a>Exportar um arquivo de configuração de aplicação

1. Abra sua *. csproj* de ficheiros e adicione o seguinte script:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Adicionar a *ConnectionString* associado a seu arquivo de configuração de aplicação como uma variável de ambiente.

2. Open *Program.cs*e atualizar a `CreateWebHostBuilder` método a utilizar o ficheiro JSON exportado ao chamar o `config.AddJsonFile()` método.

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

### <a name="build-and-run-the-app-locally"></a>Criar e executar a aplicação localmente

1. Definir uma variável de ambiente com o nome **ConnectionString**e defina-o para a chave de acesso ao seu arquivo de configuração de aplicação. Se utilizar a linha de comandos do Windows, execute o seguinte comando e reinicie o prompt de comando para permitir que a alteração tenha efeito:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se utilizar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se usar o macOS ou Linux, execute o seguinte comando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Para criar a aplicação com a CLI do .NET Core, execute o seguinte comando na shell de comandos:

        dotnet build

3. Após a compilação for concluída com êxito, execute o seguinte comando para executar a aplicação web localmente:

        dotnet run

4. Abra uma janela do browser e aceda a `http://localhost:5000`, que é o URL predefinido da aplicação web alojada localmente.

    ![Início rápido iniciação da aplicação local](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, exportar dados de configuração de aplicações do Azure para ser usado num pipeline de implementação. Para saber mais sobre como utilizar a configuração de aplicações, avance para os exemplos da CLI do Azure.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./howto-integrate-azure-managed-service-identity.md)
