---
title: Início rápido para configuração de Azure App com o .NET Core | Microsoft Docs
description: Um guia de início rápido para usar a configuração de Azure App com aplicativos .NET Core
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: 4d9ea8c7cc64a27ec6033ff084d5771328f89699
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714669"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Início rápido: criar um aplicativo .NET Core com a configuração do aplicativo

Neste guia de início rápido, você incorpora a configuração de Azure App em um aplicativo de console do .NET Core para centralizar o armazenamento e o gerenciamento de configurações de aplicativo separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download) - também disponível na [Casca de Nuvem Azure](https://shell.azure.com).

## <a name="create-an-app-configuration-store"></a>Criar um repositório de configurações de aplicativo

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione O Explorador de **Configuração** > **Criar** para adicionar os seguintes pares de valor-chave:

    | Chave | Valor |
    |---|---|
    | TestApp: configurações: mensagem | Dados da configuração Azure App |

    Deixe o **rótulo** e o **tipo de conteúdo** vazios por enquanto.

## <a name="create-a-net-core-console-app"></a>Criar um aplicativo de console do .NET Core

Utiliza a interface de [linha de comando .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) para criar um novo projeto de aplicação de consola .NET Core. A vantagem de usar o CLI do .NET Core sobre o Visual Studio é que ele está disponível nas plataformas Windows, macOS e Linux.  Em alternativa, utilize as ferramentas pré-instaladas disponíveis na [Casca de Nuvem Azure](https://shell.azure.com).

1. Crie uma pasta nova para o projeto.

2. Na nova pasta, execute o seguinte comando para criar um novo projeto de aplicativo de console ASP.NET Core:

    ```CLI
        dotnet new console
    ```

## <a name="connect-to-an-app-configuration-store"></a>Conectar-se a um repositório de configuração de aplicativo

1. Adicione uma referência ao pacote nuGet `Microsoft.Extensions.Configuration.AzureAppConfiguration` executando o seguinte comando:

    ```CLI
        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 3.0.0-preview-011100001-1152
    ```

2. Execute o seguinte comando para restaurar os pacotes para seu projeto:

    ```CLI
        dotnet restore
    ```

3. Abra *Program.cs*e adicione uma referência ao fornecedor de configuração de aplicações .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Atualize o método `Main` para utilizar a Configuração da App, ligando para o método `builder.AddAzureAppConfiguration()`.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Detete uma variável ambiental chamada **ConnectionString**e detetete-a na chave de acesso à sua loja de configuração de aplicações. Na linha de comando, execute o seguinte comando e reinicie o prompt de comando para permitir que a alteração entre em vigor:

    ```CLI
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se você usar o Windows PowerShell, execute o seguinte comando:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se você usar o macOS ou Linux, execute o seguinte comando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Execute o seguinte comando para compilar o aplicativo de console:

    ```CLI
        dotnet build
    ```

3. Depois que a compilação for concluída com êxito, execute o seguinte comando para executar o aplicativo localmente:

    ```CLI
        dotnet run
    ```

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste quickstart, criou uma nova loja de configuração de aplicações e utilizou-a com uma aplicação de consola .NET Core através do fornecedor de configuração de [aplicações.](https://go.microsoft.com/fwlink/?linkid=2074664) Para saber como configurar seu aplicativo .NET Core para atualizar dinamicamente as definições de configuração, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Ativar a configuração dinâmica](./enable-dynamic-configuration-dotnet-core.md)
