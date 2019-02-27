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
ms.openlocfilehash: 01302bf206c4205abca23cb90684e0672215eeda
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884848"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Início rápido: Criar um .NET Framework de aplicação com a configuração de aplicações do Azure

Configuração de aplicações do Azure é um serviço de configuração gerida no Azure. Permite-lhe armazenar e gerir todas as suas definições de aplicação num único local que é separada do seu código facilmente. Este guia de introdução mostra-lhe como incorporar o serviço uma aplicação de consola de ambiente de trabalho do Windows baseados no .NET Framework.

![Início rápido concluído no local](./media/quickstarts/dotnet-fx-app-run.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, instale [Visual Studio 2017](https://visualstudio.microsoft.com/vs) e [.NET Framework 4.7.1](https://dotnet.microsoft.com/download) ou posterior se ainda não o fez.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Criar um arquivo de configuração de aplicação

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>Criar uma aplicação de consola .NET

1. Inicie o Visual Studio e selecione **arquivo** > **New** > **projeto**.

2. Na **novo projeto** caixa de diálogo, selecione **instalado**, expanda **Visual C#**   >  **Windows Desktop**, selecione  **Aplicação da consola (.NET Framework)**, introduza um **Name** para o seu projeto, escolha **.NET Framework 4.7.1** ou a cópia de segurança e clique em **OK**.

## <a name="connect-to-app-configuration-store"></a>Ligar ao arquivo de configuração de aplicação

1. Clique no projeto e selecione **gerir pacotes NuGet...** . Na **navegue** separador, procurar e adicionar o seguinte pacotes NuGet ao seu projeto (Verifique a **incluir pré-lançamento** caixa se não é possível encontrá-los).
    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    ```

2. Atualização do *App. config* ficheiro do seu projeto seguinte

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
        <add key="ConnectionString" value ="First filled in by 'Environment'. Could be a dev, test, staging, or production connection string." />
    </appSettings>
    ```
   Plesae tenha em atenção, como podemos estará lendo a cadeia de ligação do seu arquivo de configuração de aplicação da variável de ambiente `ConnectionString`, é importante adicionar a `Environment` builder de configuração antes do `MyConfigStore` no `configBuilders` propriedade do `appSettings` secção.

3. Open *Program.cs* e atualizar a `Main` método a utilizar a configuração de aplicações ao chamar `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Criar e executar a aplicação localmente

1. Definir uma variável de ambiente com o nome **ConnectionString** para a cadeia de ligação do seu arquivo de configuração de aplicação. Se estiver a utilizar o Prompt de comando do Windows, execute o seguinte comando:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se estiver a utilizar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

2. Reinicie o Visual Studio para permitir que a alteração entre em vigor e, em seguida, prima **Ctrl + F5** no teclado para compilar e executar a aplicação de consola.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou um novo arquivo de configuração de aplicação e Use com uma aplicação de consola do .NET Framework. Para saber mais sobre como utilizar a configuração de aplicações, avance para o próximo tutorial que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Identidades geridas para a integração de recursos do Azure](./integrate-azure-managed-service-identity.md)
