---
title: Integrar a configuração de aplicações azure utilizando um pipeline de integração e entrega contínuo
description: Aprenda a implementar integração contínua e entrega usando a Configuração da App Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: lcozzens
ms.openlocfilehash: c744557471a9b37bd620bb9195bdb709c24649ab
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77047277"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrar num pipeline CI/CD

Este artigo explica como utilizar dados da Configuração de Aplicações Azure num sistema de integração contínua e implementação contínua.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Usar a configuração de aplicativo em seu pipeline DevOps do Azure

Se você tiver um pipeline DevOps do Azure, poderá buscar valores de chave da configuração do aplicativo e defini-los como variáveis de tarefa. A [extensão DevOps](https://go.microsoft.com/fwlink/?linkid=2091063) de Configuração de Aplicações Azure é um módulo adicional que fornece esta funcionalidade. Siga as suas instruções para utilizar a extensão numa sequência de tarefas de construção ou de libertação.

## <a name="deploy-app-configuration-data-with-your-application"></a>Implantar dados de configuração de aplicativo com seu aplicativo

Seu aplicativo poderá falhar ao ser executado se depender de Azure App configuração e não puder acessá-lo. Aumente a resiliência da sua aplicação através de dados de configuração de embalagem num ficheiro que é implantado com a aplicação e carregado localmente durante o arranque da aplicação. Esta abordagem garante que a sua aplicação tem valores de definição padrão no arranque. Esses valores são substituídos por quaisquer alterações mais recentes em um repositório de configuração de aplicativo quando ele está disponível.

Utilizando a função [exportação](./howto-import-export-data.md#export-data) da Configuração de Aplicações Azure, pode automatizar o processo de recuperação dos dados de configuração atuais como um único ficheiro. Em seguida, pode incorporar este ficheiro num passo de construção ou implantação no seu oleoduto de integração contínua e implantação contínua (CI/CD).

O exemplo a seguir mostra como incluir dados de configuração de aplicativo como uma etapa de compilação para o aplicativo Web introduzido nos guias de início rápido. Antes de continuar, termine [Criar uma aplicação ASP.NET Core com configuração](./quickstart-aspnet-core-app.md) de aplicações primeiro.

Você pode usar qualquer editor de código para executar as etapas neste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível nas plataformas Windows, macOS e Linux.

### <a name="prerequisites"></a>Pré-requisitos

Se construir localmente, faça o download e instale o [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se ainda não o fez.

Para fazer uma construção em nuvem, com Azure DevOps, por exemplo, certifique-se de que o [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) está instalado no seu sistema de construção.

### <a name="export-an-app-configuration-store"></a>Exportar um repositório de configurações de aplicativo

1. Abra o seu ficheiro *.csproj* e adicione o seguinte script:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```
1. Abra *Program.cs*e atualize o método `CreateWebHostBuilder` para utilizar o ficheiro JSON exportado, chamando o método `config.AddJsonFile()`.  Adicione o espaço de nome `System.Reflection` também.

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

1. Detete uma variável ambiental chamada **ConnectionString**e detetete-a na chave de acesso à sua loja de configuração de aplicações. 
    Se você usar o prompt de comando do Windows, execute o seguinte comando e reinicie o prompt de comando para permitir que a alteração entre em vigor:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se você usar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se você usar o macOS ou Linux, execute o seguinte comando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Para compilar o aplicativo usando o CLI do .NET Core, execute o seguinte comando no Shell de comando:

        dotnet build

3. Depois que a compilação for concluída com êxito, execute o seguinte comando para executar o aplicativo Web localmente:

        dotnet run

4. Abra uma janela do navegador e vá para `http://localhost:5000`, que é o URL padrão para a aplicação web hospedada localmente.

    ![Local de inicialização do aplicativo de início rápido](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você exportou Azure App dados de configuração a serem usados em um pipeline de implantação. Para saber mais sobre como usar a configuração de aplicativo, prossiga para os exemplos de CLI do Azure.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./howto-integrate-azure-managed-service-identity.md)
