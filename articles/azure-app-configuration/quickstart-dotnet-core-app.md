---
title: Início rápido para a configuração de aplicações do Azure com .NET Core | Documentos da Microsoft
description: Um guia de introdução para utilizar a configuração de aplicações do Azure com aplicações de .NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 5501e92b9a9d977f74bf4ed028b3cd3de4e56133
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225387"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Início rápido: Criar um .NET Core de aplicação com a configuração de aplicações

Configuração de aplicações do Azure é um serviço de configuração gerida no Azure. Pode usá-lo facilmente armazenar e gerir todas as suas definições de aplicação num único local que é separada a partir do código. Este guia de introdução mostra-lhe como incorporar o serviço uma aplicação de consola .NET Core.

Pode utilizar qualquer editor de código para realizar os passos neste guia de introdução. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível no Windows, macOS e plataformas Linux.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este início rápido, instale o [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Criar um arquivo de configuração de aplicação

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-core-console-app"></a>Criar uma aplicação de consola .NET Core

Utilizar o [.NET Core interface de linha de comandos (CLI)](https://docs.microsoft.com/dotnet/core/tools/) para criar um novo projeto de aplicação de consola .NET Core. A vantagem de utilizar a CLI do .NET Core ao longo do Visual Studio é que ele está disponível para todas as plataformas de Linux, macOS e Windows.

1. Crie uma pasta nova para o projeto.

2. Na nova pasta, execute o seguinte comando para criar um novo projeto de aplicação web de MVC do ASP.NET Core:

        dotnet new console

## <a name="connect-to-an-app-configuration-store"></a>Ligar a um arquivo de configuração de aplicação

1. Adicionar uma referência para o `Microsoft.Extensions.Configuration.AzureAppConfiguration` pacote NuGet ao executar o seguinte comando:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. Execute o seguinte comando para restaurar pacotes para o seu projeto:

        dotnet restore

3. Open *Program.cs*e atualizar a `Main` método a utilizar a configuração de aplicações ao chamar o `builder.AddAzureAppConfiguration()` método.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Criar e executar a aplicação localmente

1. Definir uma variável de ambiente com o nome **ConnectionString**e defina-o para a chave de acesso ao seu arquivo de configuração de aplicação. Se utilizar a linha de comandos do Windows, execute o seguinte comando e reinicie o prompt de comando para permitir que a alteração tenha efeito:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se utilizar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se usar o macOS ou Linux, execute o seguinte comando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Execute o seguinte comando para criar a aplicação de consola:

        dotnet build

3. Após a compilação for concluída com êxito, execute o seguinte comando para executar a aplicação localmente:

        dotnet run

    ![Execução manual de início rápido da aplicação](./media/quickstarts/dotnet-core-app-run.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou um novo arquivo de configuração de aplicação e Use com uma aplicação de consola .NET Core. Para saber mais sobre como utilizar a configuração de aplicações, avance para o próximo tutorial que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Identidades geridas para a integração de recursos do Azure](./integrate-azure-managed-service-identity.md)
