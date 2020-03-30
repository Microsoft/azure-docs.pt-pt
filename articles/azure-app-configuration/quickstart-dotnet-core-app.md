---
title: Quickstart para Configuração de Aplicações Azure com .NET Core [ Núcleo NET ] Microsoft Docs
description: Um arranque rápido para usar a configuração da app Azure com aplicações .NET Core
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: 420d9b48013f5f6debe588667fe1cc0390517e66
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245383"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Quickstart: Criar uma aplicação .NET Core com configuração de aplicações

Neste arranque rápido, incorpora a Configuração de Aplicações Azure numa aplicação de consola .NET Core para centralizar o armazenamento e a gestão das definições de aplicações separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download) - também disponível na [Casca de Nuvem Azure](https://shell.azure.com).

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicações

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Select Configuration Explorer** > **Create** > **Key-value** para adicionar os seguintes pares de valor-chave:

    | Chave | Valor |
    |---|---|
    | TestApp:Definições:Mensagem | Dados da Configuração de Aplicações Azure |

    Deixe o **rótulo** e o **tipo de conteúdo** vazios por enquanto.

7. Selecione **Aplicar**.

## <a name="create-a-net-core-console-app"></a>Criar uma aplicação de consola .NET Core

Utiliza a interface de [linha de comando .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) para criar um novo projeto de aplicação de consola .NET Core. A vantagem de utilizar o .NET Core CLI sobre o Visual Studio é que está disponível nas plataformas Windows, macOS e Linux.  Em alternativa, utilize as ferramentas pré-instaladas disponíveis na [Casca de Nuvem Azure](https://shell.azure.com).

1. Crie uma pasta nova para o projeto.

2. Na nova pasta, execute o seguinte comando para criar um novo ASP.NET projeto de aplicação de consola Core:

    ```dotnetcli
    dotnet new console
    ```

## <a name="connect-to-an-app-configuration-store"></a>Ligar a uma loja de configuração de aplicações

1. Adicione uma referência `Microsoft.Extensions.Configuration.AzureAppConfiguration` ao pacote NuGet executando o seguinte comando:

    ```dotnetcli
    dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration
    ```

2. Execute o seguinte comando para restaurar as embalagens para o seu projeto:

    ```dotnetcli
    dotnet restore
    ```

3. Abra *Program.cs*e adicione uma referência ao fornecedor de configuração de aplicações .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Atualize `Main` o método para utilizar `builder.AddAzureAppConfiguration()` a Configuração da Aplicação, ligando para o método.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Construir e executar a app localmente

1. Detete uma variável ambiental chamada **ConnectionString**e detetete-a na chave de acesso à sua loja de configuração de aplicações. Na linha de comando, executar o seguinte comando:

    ```cmd
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o Windows PowerShell, execute o seguinte comando:

    ```azurepowershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar macOS ou Linux, execute o seguinte comando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

    Reinicie a solicitação de comando para permitir que a alteração faça efeito. Imprima o valor da variável ambiental para validar que está corretamente definida.

2. Executar o seguinte comando para construir a aplicação de consola:

    ```dotnetcli
    dotnet build
    ```

3. Depois de a construção concluída com sucesso, execute o seguinte comando para executar a app localmente:

    ```dotnetcli
    dotnet run
    ```

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou uma nova loja de configuração de aplicações e utilizou-a com uma aplicação de consola .NET Core através do fornecedor de configuração de [aplicações.](https://go.microsoft.com/fwlink/?linkid=2074664) Para aprender a configurar a sua aplicação .NET Core para atualizar dinamicamente as definições de configuração, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Ativar a configuração dinâmica](./enable-dynamic-configuration-dotnet-core.md)
