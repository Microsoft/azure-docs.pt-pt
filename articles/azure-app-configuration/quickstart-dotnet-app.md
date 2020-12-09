---
title: Quickstart para Configuração de Aplicativos Azure com .NET Framework / Microsoft Docs
description: Neste artigo, crie uma aplicação .NET Framework com Configuração de Aplicações Azure para centralizar o armazenamento e gestão das definições de aplicações separadas do seu código.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 62516218ed2c0249f829ad8d286e4ad8bbc471f8
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932096"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Quickstart: Criar uma aplicação .NET Framework com Configuração de Aplicações Azure

Neste arranque rápido, incorpora a Configuração de Aplicações Azure numa aplicação de consola baseada em .NET Framework para centralizar o armazenamento e gestão das definições de aplicações separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.Net Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecione **Configuration Explorer**  >  **Crie**  >  **valor-chave** para adicionar os seguintes pares de valor-chave:

    | Chave | Valor |
    |---|---|
    | TestApp:Definições:Mensagem | Dados da Configuração da Aplicação Azure |

    Deixe **a etiqueta** e o tipo de **conteúdo** vazios por enquanto.

8. Selecione **Aplicar**.

## <a name="create-a-net-console-app"></a>Criar uma aplicação de consola .NET

1. Inicie o Estúdio Visual e selecione **File**  >  **New**  >  **Project**.

1. In **Create a new project**, filter on the **Console** project type and click on Console **App (.NET Framework)**. Selecione **Seguinte**.

1. Em **Configurar o seu novo projeto,** insira um nome de projeto. No **quadro,** selecione **o quadro .NET 4.7.1** ou superior. Selecione **Criar**.

## <a name="connect-to-an-app-configuration-store"></a>Conecte-se a uma loja de configuração de aplicativos

1. Clique com o botão direito no seu projeto e **selecione Gerir Pacotes NuGet**. No separador Procurar, procure e adicione os **seguintes** pacotes NuGet ao seu projeto.

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

   A cadeia de ligação da sua loja de configuração de aplicações é lida a partir da variável `ConnectionString` ambiente. Adicione o `Environment` construtor de configuração antes `MyConfigStore` da propriedade da `configBuilders` `appSettings` secção.

1. Abra *Program.cs*, e atualize o `Main` método para utilizar a Configuração da Aplicação chamando `ConfigurationManager` .

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
        Console.ReadKey();
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Construa e execute a app localmente

1. Atualize o ficheiro **App.config** substituindo o `${ConnectionString}` fio de ligação real à sua instância de Configuração de Aplicação. Pode encontrá-lo no separador **Teclas de Acesso** do recurso de Configuração de Aplicação no Portal Azure.

1. Prima Ctrl + F5 para construir e executar a aplicação da consola.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma nova loja de Configuração de Aplicações e utilizou-a com uma aplicação de consola .NET Framework. O valor `AppSettings` do não vai mudar depois do início da `ConfigurationManager` candidatura. A biblioteca de fornecedores de configuração padrão de configuração da aplicação .NET, no entanto, também pode ser usada numa aplicação .NET Framework. Para aprender a permitir que a sua aplicação .NET Framework atualização dinâmica das definições de configuração, continue até ao próximo tutorial.

> [!div class="nextstepaction"]
> [Ativar a configuração dinâmica](./enable-dynamic-configuration-dotnet.md)
