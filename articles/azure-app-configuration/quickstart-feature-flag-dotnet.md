---
title: Quickstart para adicionar bandeiras de recurso às aplicações .NET Framework  Microsoft Docs  Docs Microsoft Docs
description: Um arranque rápido para adicionar bandeiras de recurso a aplicações .NET Framework e geri-las na Configuração de Aplicações Azure
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
ms.openlocfilehash: 5ea9749c07aadc7037e753160e9b053992bebae2
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619330"
---
# <a name="quickstart-add-feature-flags-to-a-net-framework-app"></a>Quickstart: Adicione bandeiras de recurso a uma aplicação .NET Framework

Neste arranque rápido, incorpora a Configuração de Aplicações Azure numa aplicação .NET Framework para criar uma implementação de ponta a ponta da gestão de funcionalidades. Pode utilizar o serviço de Configuração de Aplicações para armazenar centralmente todas as suas bandeiras de funcionalidades e controlar os seus estados. 

As bibliotecas .NET Feature Management alargam o quadro com um suporte abrangente de bandeira de recurso. Estas bibliotecas são construídas em cima do sistema de configuração .NET. Integram-se perfeitamente com a Configuração da App através do seu fornecedor de configuração .NET.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Estúdio Visual 2019](https://visualstudio.microsoft.com/vs)
- [.Quadro líquido 4.8](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicações

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione Gestor de **Funcionalidades** >  **+Adicione** para adicionar uma bandeira de recurso chamada `Beta`.

    > [!div class="mx-imgBorder"]
    > ![ativar a bandeira de recurso chamada Beta](media/add-beta-feature-flag.png)

    Deixe `label` indefinida por enquanto.

## <a name="create-a-net-console-app"></a>Criar uma aplicação de consola .NET

1. Inicie o Estúdio Visual e selecione **File** > **New** > **Project**.

1. Em **Criar um novo projeto,** filtre no tipo de projeto da **Consola** e clique na App **consola (.NET Framework)** . Clique em **Seguinte**.

1. Em **Configure o seu novo projeto,** insira um nome de projeto. **No quadro**, selecione **.NET Quadro 4.8** ou superior. Clique em **Criar**.

## <a name="connect-to-an-app-configuration-store"></a>Ligar a uma loja de configuração de aplicações

1. Clique no seu projeto e selecione **Gerir pacotes NuGet**. No separador **Browse,** procure e adicione os seguintes pacotes NuGet ao seu projeto. Se não os encontrar, selecione a caixa de verificação **de pré-lançamento Incluir.**

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
    ```

1. Atualize o método `Main` para se ligar à Configuração da Aplicação, especificando a opção `UseFeatureFlags` para que as bandeiras sejam recuperadas. Em seguida, mostrar uma mensagem se a bandeira de características `Beta` estiver ativada.

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
        }
    ```

## <a name="build-and-run-the-app-locally"></a>Construir e executar a app localmente

1. Detete uma variável ambiental chamada **ConnectionString** para a cadeia de ligação da sua loja de configuração de aplicações. Se utilizar o pedido de comando do Windows, execute o seguinte comando:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se utilizar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Reiniciar o Estúdio Visual para permitir que a alteração faça efeito. 

1. Prima Ctrl + F5 para construir e executar a aplicação de consola.

    ![App com bandeira de recurso ativada](./media/quickstarts/dotnet-app-feature-flag.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma bandeira de funcionalidade na Configuração de Aplicações e utilizou-a com uma aplicação de consola .NET Framework. Para aprender a atualizar dinamicamente as bandeiras de recurso e outros valores de configuração sem reiniciar a aplicação, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Ativar a configuração dinâmica](./enable-dynamic-configuration-dotnet.md)
