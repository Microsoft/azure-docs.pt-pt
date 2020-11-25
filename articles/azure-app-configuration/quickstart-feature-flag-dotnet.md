---
title: Quickstart para adicionar bandeiras de recurso às aplicações .NET Framework Microsoft Docs ! Microsoft Docs
description: Um quickstart para adicionar bandeiras de funcionalidades a aplicações .NET Framework e geri-las na Configuração de Aplicações Azure
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 10/19/2020
ms.author: lcozzens
ms.openlocfilehash: c379d3f99628c2d3fb32ae34ca0214f608d365c7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012702"
---
# <a name="quickstart-add-feature-flags-to-a-net-framework-app"></a>Quickstart: Adicione bandeiras de recurso a uma aplicação .NET Framework

Neste quickstart, incorpora a Configuração de Aplicações Azure numa aplicação .NET Framework para criar uma implementação de ponta a ponta da gestão de funcionalidades. Pode utilizar o serviço de Configuração de Aplicações para armazenar centralmente todas as suas bandeiras de funcionalidades e controlar os seus estados. 

As bibliotecas de Gestão de Recursos .NET alargam o quadro com suporte de bandeira de recurso. Estas bibliotecas são construídas em cima do sistema de configuração .NET. Integram-se com a Configuração de Aplicações através do seu fornecedor de configuração .NET.

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.8](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecione **Feature Manager**  >  **+Adicione** para adicionar uma bandeira de recurso chamada `Beta` .

    > [!div class="mx-imgBorder"]
    > ![Ativar a bandeira de recurso chamada Beta](media/add-beta-feature-flag.png)

    Deixe `label` indefinida por enquanto.

## <a name="create-a-net-console-app"></a>Criar uma aplicação de consola .NET

1. Inicie o Estúdio Visual e selecione **File**  >  **New**  >  **Project**.

1. In **Create a new project**, filter on the **Console** project type and click on Console **App (.NET Framework)**. Clique em **Seguinte**.

1. Em **Configurar o seu novo projeto,** insira um nome de projeto. No **quadro,** **selecione .NET Framework 4.8** ou superior. Clique em **Criar**.

## <a name="connect-to-an-app-configuration-store"></a>Conecte-se a uma loja de configuração de aplicativos

1. Clique com o botão direito no seu projeto e **selecione Gerir Pacotes NuGet**. No separador Procurar, procure e adicione os **seguintes** pacotes NuGet ao seu projeto. Se não conseguir encontrá-los, selecione a caixa de verificação **"Incluir pré-relançar".**

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration.AzureAppConfiguration
    Microsoft.FeatureManagement
    ```

1. Abra *Program.cs* e adicione as seguintes declarações:

    ```csharp
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    using Microsoft.FeatureManagement;
    using System.Threading.Tasks;
    ```

1. Atualize o `Main` método para ligar à Configuração da Aplicação, especificando a `UseFeatureFlags` opção para que as bandeiras de recurso sejam recuperadas. Em seguida, apresente uma mensagem se a bandeira de `Beta` funcionalidade estiver ativada.

    ```csharp
        public static async Task Main(string[] args)
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
            Console.WriteLine("Press any key to continue ...");
            Console.Read();
        }
    ```

## <a name="build-and-run-the-app-locally"></a>Construa e execute a app localmente

1. Desaprova uma variável ambiental chamada **ConnectionString** para a cadeia de ligação da sua loja de Configuração de Aplicações. Se utilizar o pedido de comando do Windows, execute o seguinte comando:

    ```console
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o Windows PowerShell, executar o seguinte comando:

    ```powershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. Reinicie o Estúdio Visual para permitir que a alteração entre em vigor. 

1. Prima Ctrl + F5 para construir e executar a aplicação da consola.

    ![App com bandeira de recurso ativada](./media/quickstarts/dotnet-app-feature-flag.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma bandeira de recurso na Configuração de Aplicações e utilizou-a com uma aplicação de consola .NET Framework. Para aprender a atualizar dinamicamente as bandeiras de funcionalidades e outros valores de configuração sem reiniciar a aplicação, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Ativar a configuração dinâmica](./enable-dynamic-configuration-dotnet.md)
