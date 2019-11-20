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
ms.openlocfilehash: 986bda4ab95985820e61c93bbe82431fb50c983b
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185020"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Início rápido: criar um aplicativo .NET Core com a configuração do aplicativo

Neste guia de início rápido, você incorpora a configuração de Azure App em um aplicativo de console do .NET Core para centralizar o armazenamento e o gerenciamento de configurações de aplicativo separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [SDK .NET Core](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Criar um repositório de configurações de aplicativo

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Configuration Explorer** >  **+ criar** para adicionar os seguintes pares de chave-valor:

    | Chave | Valor |
    |---|---|
    | TestApp: configurações: mensagem | Dados da configuração Azure App |

    Deixe **rótulo** e **tipo de conteúdo** vazio por enquanto.

## <a name="create-a-net-core-console-app"></a>Criar um aplicativo de console do .NET Core

Você usa a [CLI (interface de linha de comando) do .NET Core](https://docs.microsoft.com/dotnet/core/tools/) para criar um novo projeto de aplicativo de console do .NET Core. A vantagem de usar o CLI do .NET Core sobre o Visual Studio é que ele está disponível nas plataformas Windows, macOS e Linux.

1. Crie uma pasta nova para o projeto.

2. Na nova pasta, execute o seguinte comando para criar um novo projeto de aplicativo de console ASP.NET Core:

        dotnet new console

## <a name="connect-to-an-app-configuration-store"></a>Conectar-se a um repositório de configuração de aplicativo

1. Adicione uma referência ao pacote `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet executando o seguinte comando:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 2.0.0-preview-009470001-1371

2. Execute o seguinte comando para restaurar os pacotes para seu projeto:

        dotnet restore

3. Abra *Program.cs*e adicione uma referência ao provedor de configuração do aplicativo .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Atualize o método de `Main` para usar a configuração de aplicativo chamando o método `builder.AddAzureAppConfiguration()`.

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

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um novo repositório de configuração de aplicativo e o utilizou com um aplicativo de console do .NET Core por meio do [provedor de configuração de aplicativo](https://go.microsoft.com/fwlink/?linkid=2074664). Para saber como configurar seu aplicativo .NET Core para atualizar dinamicamente as definições de configuração, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Habilitar configuração dinâmica](./enable-dynamic-configuration-dotnet-core.md)
