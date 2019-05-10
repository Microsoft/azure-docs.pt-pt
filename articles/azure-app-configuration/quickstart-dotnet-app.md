---
title: Início rápido para a configuração de aplicações do Azure com o .NET Framework | Documentos da Microsoft
description: Um guia de introdução para utilizar a configuração de aplicações do Azure com aplicações de .NET Framework
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
ms.openlocfilehash: acf1608257bc5416ff52ed9fbd4209008712f2d7
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408698"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Início rápido: Criar um .NET Framework de aplicação com a configuração de aplicações do Azure

Configuração de aplicações do Azure é um serviço de configuração gerida no Azure. Pode usá-lo facilmente armazenar e gerir todas as suas definições de aplicação num único local que é separada a partir do código. Este guia de introdução mostra-lhe como incorporar o serviço uma aplicação de consola de ambiente de trabalho do Windows baseados no .NET Framework.

![Local completa do guia de introdução](./media/quickstarts/dotnet-fx-app-run.png)

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este início rápido, instale [Visual Studio 2017](https://visualstudio.microsoft.com/vs) e [.NET Framework 4.7.1](https://dotnet.microsoft.com/download) ou posterior se ainda não o fez.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Criar um arquivo de configuração de aplicação

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Explorador de configuração** > **+ criar** para adicionar os seguintes pares de chave-valor:

    | Chave | Value |
    |---|---|
    | TestApp:Settings:Message | Dados de configuração de aplicações do Azure |

    Deixe **rótulo** e **tipo de conteúdo** branco por agora.

## <a name="create-a-net-console-app"></a>Criar uma aplicação de consola .NET

1. Inicie o Visual Studio e selecione **arquivo** > **New** > **projeto**.

2. Na **novo projeto**, selecione **instalado** > **Visual C#**   >  **Windows Desktop**. Selecione **aplicação de consola (.NET Framework)** e introduza um nome para o seu projeto. Selecione **.NET Framework 4.7.1** ou cópias de segurança e selecione **OK**.

## <a name="connect-to-an-app-configuration-store"></a>Ligar a um arquivo de configuração de aplicação

1. Clique no projeto e selecione **gerir pacotes NuGet**. Sobre o **procurar** separador, procure e adicione os seguintes pacotes NuGet ao seu projeto. Se não é possível encontrá-los, selecione o **incluir pré-lançamento** caixa de verificação.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    ```

2. Atualização do *App. config* ficheiro do seu projeto da seguinte forma:

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

   A cadeia de ligação do seu arquivo de configuração de aplicação é de leitura da variável de ambiente `ConnectionString`. Adicionar a `Environment` builder de configuração antes do `MyConfigStore` no `configBuilders` propriedade do `appSettings` secção.

3. Open *Program.cs*e atualizar a `Main` método a utilizar a configuração de aplicações ao chamar `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Criar e executar a aplicação localmente

1. Definir uma variável de ambiente com o nome **ConnectionString** para a cadeia de ligação do seu arquivo de configuração de aplicação. Se utilizar a linha de comandos do Windows, execute o seguinte comando:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se utilizar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

2. Reinicie o Visual Studio para permitir que a alteração tenha efeito. Prima Ctrl + F5 para compilar e executar a aplicação de consola.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou um novo arquivo de configuração de aplicação e Use com uma aplicação de consola do .NET Framework. Para saber mais sobre como utilizar a configuração de aplicações, avance para o próximo tutorial que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./howto-integrate-azure-managed-service-identity.md)
