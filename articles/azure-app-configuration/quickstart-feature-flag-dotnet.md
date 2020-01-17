---
title: Início rápido para adição de sinalizadores de recurso a aplicativos .NET Framework | Microsoft Docs | Microsoft Docs
description: Um guia de início rápido para adicionar sinalizadores de recursos para .NET Framework aplicativos e gerenciá-los na configuração do Azure App
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
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: bdb00bfbadec68fa110f747858d264a2c34f8bd1
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76120874"
---
# <a name="quickstart-add-feature-flags-to-a-net-framework-app"></a>Início rápido: Adicionar sinalizadores de recurso a um aplicativo .NET Framework

Neste guia de início rápido, você incorpora Azure App configuração em um aplicativo .NET Framework para criar uma implementação de ponta a ponta do gerenciamento de recursos. Você pode usar o serviço de configuração de aplicativo para armazenar centralmente todos os sinalizadores de recursos e controlar seus Estados. 

As bibliotecas de gerenciamento de recursos do .NET estendem a estrutura com suporte abrangente ao sinalizador de recursos. Essas bibliotecas são criadas com base no sistema de configuração do .NET. Eles se integram perfeitamente com a configuração do aplicativo por meio de seu provedor de configuração do .NET.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Criar um repositório de configurações de aplicativo

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>Criar um aplicativo de console .NET

1. Inicie o Visual Studio e selecione **arquivo** > **novo** **projeto**de > .

1. Em **criar um novo projeto**, filtre o tipo de projeto de **console** e clique em **aplicativo de console (.NET Framework)** . Clique em **Seguinte**.

1. Em **configurar seu novo projeto**, insira um nome de projeto. Em **estrutura**, selecione **.NET Framework 4.7.1** ou superior. Clique em **Criar**.

## <a name="connect-to-an-app-configuration-store"></a>Conectar-se a um repositório de configuração de aplicativo

1. Clique com o botão direito do mouse em seu projeto e selecione **gerenciar pacotes NuGet**. Na guia **procurar** , pesquise e adicione os seguintes pacotes NuGet ao seu projeto. Se você não conseguir encontrá-los, marque a caixa de seleção **incluir pré-lançamento** .

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration.AzureAppConfiguration
    Microsoft.FeatureManagement
    ```

1. Abra *Program.cs* e adicione as seguintes instruções:

    ```csharp
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    using Microsoft.FeatureManagement;
    ```

1. Atualize o método `Main` para se conectar à configuração do aplicativo, especificando a opção `UseFeatureFlags` para que os sinalizadores de recursos sejam recuperados. Em seguida, exiba uma mensagem se o sinalizador de recurso `Beta` estiver habilitado.

    ```csharp
        public static void Main(string[] args)
        {
            AsyncMain().Wait();
        }

        private static async Task AsyncMain()
        {
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags();
                }).Build();

            IServiceCollection services = new ServiceCollection();

            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement();

            using (ServiceProvider serviceProvider = services.BuildServiceProvider())
            {
                IFeatureManager featureManager = serviceProvider.GetRequiredService<IFeatureManager>();

                if (await featureManager.IsEnabledAsync("Beta"))
                {
                    Console.WriteLine("Welcome to the beta!");
                }
            }

            Console.WriteLine("Hello World!");
        }
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Defina uma variável de ambiente chamada **ConnectionString** para a cadeia de conexão de seu repositório de configurações de aplicativo. Se você usar o prompt de comando do Windows, execute o seguinte comando:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se você usar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Reinicie o Visual Studio para permitir que a alteração entre em vigor. 

1. Pressione CTRL + F5 para compilar e executar o aplicativo de console.

    ![Aplicativo com sinalizador de recurso habilitado](./media/quickstarts/dotnet-app-feature-flag.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou um sinalizador de recurso na configuração do aplicativo e o utilizou com um aplicativo de console .NET Framework. Para saber como atualizar dinamicamente os sinalizadores de recursos e outros valores de configuração sem reiniciar o aplicativo, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Habilitar configuração dinâmica](./enable-dynamic-configuration-dotnet.md)