---
title: Quickstart para Configuração de Aplicações Azure com .NET Framework [ Microsoft Docs
description: Um arranque rápido para usar a configuração da app Azure com aplicações .NET Framework
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: lcozzens
ms.openlocfilehash: cfa89a7921751541d1044d697237946cd63cbfd8
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82732058"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Quickstart: Criar uma app .NET Framework com configuração de app Azure

Neste arranque rápido, incorpora a Configuração de Aplicações Azure numa aplicação de consola baseada em .NET para centralizar o armazenamento e a gestão das definições de aplicações separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.Quadro líquido 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicações

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Select Configuration Explorer** > **Create** > **Key-value** para adicionar os seguintes pares de valor-chave:

    | Chave | Valor |
    |---|---|
    | TestApp:Definições:Mensagem | Dados da Configuração de Aplicações Azure |

    Deixe o **rótulo** e o **tipo de conteúdo** vazios por enquanto.

7. Selecione **Aplicar**.

## <a name="create-a-net-console-app"></a>Criar uma aplicação de consola .NET

1. Inicie o Estúdio Visual e selecione **File** > **New** > **Project**.

1. Em **Criar um novo projeto,** filtre no tipo de projeto da **Consola** e clique na App **consola (.NET Framework)**. Selecione **Seguinte**.

1. Em **Configure o seu novo projeto,** insira um nome de projeto. **No quadro**, selecione **.NET Quadro 4.7.1** ou superior. Selecione **Criar**.

## <a name="connect-to-an-app-configuration-store"></a>Ligar a uma loja de configuração de aplicações

1. Clique no seu projeto e selecione **Gerir pacotes NuGet**. No separador **Browse,** procure e adicione os seguintes pacotes NuGet ao seu projeto.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. Atualize o ficheiro *App.config* do seu projeto da seguinte forma:

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   A cadeia de ligação da sua loja `ConnectionString`de configuração de aplicações é lida a partir da variável ambiente . Adicione `Environment` o construtor `MyConfigStore` de `configBuilders` configuração `appSettings` antes da propriedade da secção.

1. Abra *Program.cs*e `Main` atualize o método `ConfigurationManager`para utilizar a Configuração da Aplicação através da chamada .

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Construir e executar a app localmente

1. Detete uma variável ambiental chamada **ConnectionString** para a cadeia de ligação da sua loja de configuração de aplicações. Se utilizar o pedido de comando do Windows, execute o seguinte comando:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o Windows PowerShell, execute o seguinte comando:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```
1. Reiniciar o Estúdio Visual para permitir que a alteração faça efeito. Prima Ctrl + F5 para construir e executar a aplicação de consola.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma nova loja de configuração de aplicações e utilizou-a com uma aplicação de consola .NET Framework. O `AppSettings` valor `ConfigurationManager` não mudará após o início da aplicação. A biblioteca de fornecedores de configuração .NET Standard configuração da App Configuração, no entanto, também pode ser usada numa aplicação .NET Framework. Para aprender como permitir que a sua aplicação .NET Framework aprenda dinamicamente as definições de configuração, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Ativar a configuração dinâmica](./enable-dynamic-configuration-dotnet.md)
