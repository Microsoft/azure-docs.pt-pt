---
title: Início rápido para configuração de Azure App com o .NET Core | Microsoft Docs
description: Um guia de início rápido para usar a configuração de Azure App com aplicativos .NET Core
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
ms.openlocfilehash: 99a42102b30c91b7f1bba06cfaae7662b408b784
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326556"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Início rápido: Criar um aplicativo .NET Core com configuração de aplicativo

Neste guia de início rápido, você incorpora a configuração de Azure App em um aplicativo de console do .NET Core para centralizar o armazenamento e o gerenciamento de configurações de aplicativo separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [SDK .NET Core](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Criar um repositório de configurações de aplicativo

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Configuration Explorer** >  **+ criar** para adicionar os seguintes pares de chave-valor:

    | Chave | Value |
    |---|---|
    | TestApp: configurações: mensagem | Dados da configuração Azure App |

    Deixe **rótulo** e **tipo de conteúdo** vazio por enquanto.

## <a name="create-a-net-core-console-app"></a>Criar um aplicativo de console do .NET Core

Você usa a [CLI (interface de linha de comando) do .NET Core](https://docs.microsoft.com/dotnet/core/tools/) para criar um novo projeto de aplicativo de console do .NET Core. A vantagem de usar o CLI do .NET Core sobre o Visual Studio é que ele está disponível nas plataformas Windows, macOS e Linux.

1. Crie uma pasta nova para o projeto.

2. Na nova pasta, execute o seguinte comando para criar um novo projeto de aplicativo de console ASP.NET Core:

        dotnet new console

## <a name="connect-to-an-app-configuration-store"></a>Conectar-se a um repositório de configuração de aplicativo

1. Adicione uma referência ao `Microsoft.Azure.AppConfiguration.AspNetCore` pacote NuGet executando o seguinte comando:

        dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-009200001-7

2. Execute o seguinte comando para restaurar os pacotes para seu projeto:

        dotnet restore

3. Abra *Program.cs*e adicione uma referência ao provedor de configuração do aplicativo .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Atualize o `Main` método para usar a configuração do aplicativo chamando `builder.AddAzureAppConfiguration()` o método.

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

1. Defina uma variável de ambiente chamada **ConnectionString**e defina-a como a chave de acesso para seu repositório de configuração de aplicativo. Se você usar o prompt de comando do Windows, execute o seguinte comando e reinicie o prompt de comando para permitir que a alteração entre em vigor:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se você usar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se você usar o macOS ou Linux, execute o seguinte comando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Execute o seguinte comando para compilar o aplicativo de console:

        dotnet build

3. Depois que a compilação for concluída com êxito, execute o seguinte comando para executar o aplicativo localmente:

        dotnet run

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você criou um novo repositório de configuração de aplicativo e o utilizou com um aplicativo de console do .NET Core por meio do [provedor de configuração de aplicativo](https://go.microsoft.com/fwlink/?linkid=2074664). Para saber mais sobre como usar a configuração de aplicativo, prossiga para o próximo tutorial que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Integração de identidade gerenciada](./howto-integrate-azure-managed-service-identity.md)
