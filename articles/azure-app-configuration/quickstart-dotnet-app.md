---
title: Início rápido para configuração de Azure App com .NET Framework | Microsoft Docs
description: Um guia de início rápido para usar a configuração de Azure App com aplicativos .NET Framework
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 8aa8c8132220965d55097c4fed8ba1b2e9501301
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326535"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Início rápido: Criar um aplicativo .NET Framework com a configuração Azure App

Neste guia de início rápido, você incorpora Azure App configuração em um aplicativo de console baseado em .NET Framework para centralizar o armazenamento e o gerenciamento das configurações de aplicativo separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Criar um repositório de configurações de aplicativo

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Configuration Explorer** >  **+ criar** para adicionar os seguintes pares de chave-valor:

    | Chave | Value |
    |---|---|
    | TestApp: configurações: mensagem | Dados da configuração Azure App |

    Deixe **rótulo** e **tipo de conteúdo** vazio por enquanto.

## <a name="create-a-net-console-app"></a>Criar um aplicativo de console .NET

1. Inicie o Visual Studio e selecione **arquivo** > **novo** > **projeto**.

2. Em **novo projeto**, selecione  > **Visual C#**   >  **área de trabalho do Windows**instalado. Selecione **aplicativo de console (.NET Framework)** e insira um nome para o seu projeto. Selecione **.NET Framework 4.7.1** ou superior e selecione **OK**.

## <a name="connect-to-an-app-configuration-store"></a>Conectar-se a um repositório de configuração de aplicativo

1. Clique com o botão direito do mouse em seu projeto e selecione **gerenciar pacotes NuGet**. Na guia **procurar** , pesquise e adicione os seguintes pacotes NuGet ao seu projeto. Se você não conseguir encontrá-los, marque a caixa de seleção **incluir pré-lançamento** .

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    ```

2. Atualize o arquivo *app. config* do seu projeto da seguinte maneira:

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

   A cadeia de conexão do repositório de configuração do aplicativo é lida na variável `ConnectionString`de ambiente. Adicione o `Environment` Construtor de configuração antes `MyConfigStore` do `appSettings` na `configBuilders` propriedade da seção.

3. Abra *Program.cs*e atualize o método `Main` para usar a configuração de aplicativo chamando `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Defina uma variável de ambiente chamada **ConnectionString** para a cadeia de conexão de seu repositório de configurações de aplicativo. Se você usar o prompt de comando do Windows, execute o seguinte comando:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se você usar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

2. Reinicie o Visual Studio para permitir que a alteração entre em vigor. Pressione CTRL + F5 para compilar e executar o aplicativo de console.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você criou um novo repositório de configuração de aplicativo e o utilizou com um aplicativo de console .NET Framework. Para saber mais sobre como usar a configuração de aplicativo, prossiga para o próximo tutorial que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Integração de identidade gerenciada](./howto-integrate-azure-managed-service-identity.md)
