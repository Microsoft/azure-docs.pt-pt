---
title: Quickstart para configuração de aplicativos Azure com .NET Core | Microsoft Docs
description: Neste arranque rápido, crie uma aplicação .NET Core com Configuração de Aplicações Azure para centralizar o armazenamento e gestão das definições de aplicações separadas do seu código.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 0ff80287971365b1477be319dc7a04760687f6a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98663405"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Quickstart: Criar uma aplicação .NET Core com Configuração de Aplicações

Neste arranque rápido, incorpora a Configuração de Aplicações Azure numa aplicação de consola .NET Core para centralizar o armazenamento e gestão das definições de aplicações separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/dotnet)
- [.NET Core SDK](https://dotnet.microsoft.com/download) - também disponível na [Azure Cloud Shell](https://shell.azure.com).

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecione **Configuration Explorer**  >  **Crie**  >  **valor-chave** para adicionar os seguintes pares de valor-chave:

    | Chave | Valor |
    |---|---|
    | TestApp:Definições:Mensagem | Dados da Configuração da Aplicação Azure |

    Deixe **a etiqueta** e o tipo de **conteúdo** vazios por enquanto.

8. Selecione **Aplicar**.

## <a name="create-a-net-core-console-app"></a>Criar uma aplicação de consola .NET Core

Utiliza a [interface de linha de comando .NET Core (CLI)](/dotnet/core/tools/) para criar um novo projeto de aplicação de consola .NET Core. A vantagem de utilizar o .NET Core CLI sobre o Visual Studio é que está disponível nas plataformas Windows, macOS e Linux.  Em alternativa, utilize as ferramentas pré-instaladas disponíveis no [Azure Cloud Shell](https://shell.azure.com).

1. Crie uma pasta nova para o projeto.

2. Na nova pasta, executar o seguinte comando para criar um novo projeto de aplicação de consola .NET Core:

    ```dotnetcli
    dotnet new console
    ```

## <a name="connect-to-an-app-configuration-store"></a>Conecte-se a uma loja de configuração de aplicativos

1. Adicione uma referência ao `Microsoft.Extensions.Configuration.AzureAppConfiguration` pacote NuGet executando o seguinte comando:

    ```dotnetcli
    dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration
    ```

2. Executar o seguinte comando para restaurar pacotes para o seu projeto:

    ```dotnetcli
    dotnet restore
    ```

3. *Programa Aberto.cs*, e adicione uma referência ao fornecedor de Configuração de Aplicação .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Atualize o `Main` método para utilizar a Configuração da Aplicação chamando o `builder.AddAzureAppConfiguration()` método.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Construa e execute a app localmente

1. Desaprote uma variável ambiental chamada **ConnectionString** e desaprote-a na chave de acesso à sua loja de Configuração de Aplicações. Na linha de comando, executar o seguinte comando:

    ```cmd
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o Windows PowerShell, executar o seguinte comando:

    ```azurepowershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o macOS ou o Linux, executar o seguinte comando:

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

    Reinicie o pedido de comando para permitir que a alteração entre em vigor. Imprima o valor da variável ambiente para validar que está definido corretamente.

2. Executar o seguinte comando para construir a aplicação de consola:

    ```dotnetcli
    dotnet build
    ```

3. Após a construção concluída com sucesso, executar o seguinte comando para executar a aplicação localmente:

    ```dotnetcli
    dotnet run
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou uma nova loja de Configuração de Aplicações e utilizou-a com uma aplicação de consola .NET Core através do fornecedor de Configuração de [Aplicações.](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration) Para aprender a configurar a sua aplicação .NET Core para atualizar dinamicamente as definições de configuração, continue até ao próximo tutorial.

> [!div class="nextstepaction"]
> [Ativar a configuração dinâmica](./enable-dynamic-configuration-dotnet-core.md)