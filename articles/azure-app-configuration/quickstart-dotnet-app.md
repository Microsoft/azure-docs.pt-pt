---
title: Início rápido para configuração de Azure App com .NET Framework | Microsoft Docs
description: Um guia de início rápido para usar a configuração de Azure App com aplicativos .NET Framework
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 10/09/2019
ms.author: lcozzens
ms.openlocfilehash: 36cfe79f5c2735e6d6737d471430deb989905cdc
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185064"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Início rápido: criar um aplicativo .NET Framework com a configuração Azure App

Neste guia de início rápido, você incorpora Azure App configuração em um aplicativo de console baseado em .NET Framework para centralizar o armazenamento e o gerenciamento das configurações de aplicativo separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Criar um repositório de configurações de aplicativo

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Configuration Explorer** >  **+ criar** para adicionar os seguintes pares de chave-valor:

    | Chave | Valor |
    |---|---|
    | TestApp: configurações: mensagem | Dados da configuração Azure App |

    Deixe **rótulo** e **tipo de conteúdo** vazio por enquanto.

## <a name="create-a-net-console-app"></a>Criar um aplicativo de console .NET

1. Inicie o Visual Studio e selecione **arquivo** > **novo** **projeto**de > .

1. Em **criar um novo projeto**, filtre o tipo de projeto de **console** e clique em **aplicativo de console (.NET Framework)** . Clique em **Seguinte**.

1. Em **configurar seu novo projeto**, insira um nome de projeto. Em **estrutura**, selecione **.NET Framework 4.7.1** ou superior. Clique em **Criar**.

## <a name="connect-to-an-app-configuration-store"></a>Conectar-se a um repositório de configuração de aplicativo

1. Clique com o botão direito do mouse em seu projeto e selecione **gerenciar pacotes NuGet**. Na guia **procurar** , pesquise e adicione os seguintes pacotes NuGet ao seu projeto. Se você não conseguir encontrá-los, marque a caixa de seleção **incluir pré-lançamento** .

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. Atualize o arquivo *app. config* do seu projeto da seguinte maneira:

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

   A cadeia de conexão do repositório de configuração do aplicativo é lida na variável de ambiente `ConnectionString`. Adicione o construtor de configuração `Environment` antes da `MyConfigStore` na propriedade `configBuilders` da seção `appSettings`.

1. Abra *Program.cs*e atualize o método `Main` para usar a configuração de aplicativo chamando `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Defina uma variável de ambiente chamada **ConnectionString** para a cadeia de conexão de seu repositório de configurações de aplicativo. Se você usar o prompt de comando do Windows, execute o seguinte comando:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se você usar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Reinicie o Visual Studio para permitir que a alteração entre em vigor. Pressione CTRL + F5 para compilar e executar o aplicativo de console.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um novo repositório de configuração de aplicativo e o utilizou com um aplicativo de console .NET Framework. O valor `AppSettings` de `ConfiguratoinManager` não será alterado depois que o aplicativo for iniciado. A biblioteca do provedor de configuração do .NET Standard de configuração do aplicativo, no entanto, também pode ser usada em um aplicativo .NET Framework. Para saber como habilitar seu aplicativo .NET Framework para atualizar dinamicamente as definições de configuração, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Habilitar configuração dinâmica](./enable-dynamic-configuration-dotnet.md)
