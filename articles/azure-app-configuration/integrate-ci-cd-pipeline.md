---
title: Integrar a configuração de aplicações azure utilizando um pipeline de integração e entrega contínuo
description: Aprenda a implementar integração contínua e entrega usando a Configuração da App Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: lcozzens
ms.openlocfilehash: 4b1b9e2360f4ae1cf428133006ed08327b10cdef
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790767"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrar num pipeline CI/CD

Este artigo explica como utilizar dados da Configuração de Aplicações Azure num sistema de integração contínua e implementação contínua.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Utilize a configuração da aplicação no seu pipeline Azure DevOps

Se tiver um Pipeline Azure DevOps, pode obter valores-chave da Configuração da App e defini-los como variáveis de tarefa. A [extensão DevOps](https://go.microsoft.com/fwlink/?linkid=2091063) de Configuração de Aplicações Azure é um módulo adicional que fornece esta funcionalidade. Siga as suas instruções para utilizar a extensão numa sequência de tarefas de construção ou de libertação.

## <a name="deploy-app-configuration-data-with-your-application"></a>Implementar dados de configuração de aplicações com a sua aplicação

A sua aplicação pode não funcionar se depender da Configuração da Aplicação Azure e não conseguir alcançá-la. Aumente a resiliência da sua aplicação através de dados de configuração de embalagem num ficheiro que é implantado com a aplicação e carregado localmente durante o arranque da aplicação. Esta abordagem garante que a sua aplicação tem valores de definição padrão no arranque. Estes valores são substituídos por quaisquer alterações mais recentes numa loja de Configuração de Aplicações quando está disponível.

Utilizando a função [exportação](./howto-import-export-data.md#export-data) da Configuração de Aplicações Azure, pode automatizar o processo de recuperação dos dados de configuração atuais como um único ficheiro. Em seguida, pode incorporar este ficheiro num passo de construção ou implantação no seu oleoduto de integração contínua e implantação contínua (CI/CD).

O exemplo que se segue mostra como incluir os dados da Configuração da App como um passo de construção para a aplicação web introduzida nos quickstarts. Antes de continuar, termine [Criar uma aplicação ASP.NET Core com configuração](./quickstart-aspnet-core-app.md) de aplicações primeiro.

Pode usar qualquer editor de código para fazer os passos neste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível nas plataformas Windows, macOS e Linux.

### <a name="prerequisites"></a>Pré-requisitos

Se construir localmente, faça o download e instale o [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se ainda não o fez.

Para fazer uma construção em nuvem, com Azure DevOps, por exemplo, certifique-se de que o [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) está instalado no seu sistema de construção.

### <a name="export-an-app-configuration-store"></a>Exportar uma loja de configuração de aplicações

1. Abra o seu ficheiro *.csproj* e adicione o seguinte script:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```
1. Abra *Program.cs*e `CreateWebHostBuilder` atualize o método para utilizar o `config.AddJsonFile()` ficheiro JSON exportado, chamando o método.  Adicione `System.Reflection` o espaço de nome também.

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

### <a name="build-and-run-the-app-locally"></a>Construir e executar a app localmente

1. Detete uma variável ambiental chamada **ConnectionString**e detetete-a na chave de acesso à sua loja de configuração de aplicações. 
    Se utilizar o pedido de comando do Windows, execute o seguinte comando e reinicie a solicitação de comando para permitir que a alteração faça efeito:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se utilizar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se utilizar macOS ou Linux, execute o seguinte comando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Para construir a aplicação utilizando o CLI .NET Core, execute o seguinte comando na concha de comando:

        dotnet build

3. Depois de a construção concluída com sucesso, execute o seguinte comando para executar a aplicação web localmente:

        dotnet run

4. Abra uma janela do `http://localhost:5000`navegador e vá para , que é o URL padrão para a aplicação web hospedada localmente.

    ![Lançamento de app Quickstart local](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, exportou dados de configuração de aplicações do Azure para serem utilizados num pipeline de implantação. Para saber mais sobre como utilizar a Configuração da App, continue com as amostras do Azure CLI.

> [!div class="nextstepaction"]
> [CLI do Azure](https://docs.microsoft.com/cli/azure/appconfig?view=azure-cli-latest)
