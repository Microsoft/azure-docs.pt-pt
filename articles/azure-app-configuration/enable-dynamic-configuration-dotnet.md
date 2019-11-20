---
title: Tutorial para usar a configuração dinâmica de configuração do Azure App em um aplicativo .NET Framework | Microsoft Docs
description: Neste tutorial, você aprenderá a atualizar dinamicamente os dados de configuração para aplicativos .NET Framework
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 7cb76d5836055ce352373fa13449e27d81e84022
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185242"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Tutorial: usar a configuração dinâmica em um aplicativo .NET Framework

A biblioteca de cliente .NET de configuração de aplicativo dá suporte à atualização de um conjunto de definições de configuração sob demanda sem causar a reinicialização de um aplicativo. Isso pode ser implementado primeiro obtendo uma instância de `IConfigurationRefresher` das opções do provedor de configuração e, em seguida, chamando `Refresh` nessa instância em qualquer lugar em seu código.

Para manter as configurações atualizadas e evitar muitas chamadas para o repositório de configuração, um cache é usado para cada configuração. Até que o valor em cache de uma configuração tenha expirado, a operação de atualização não atualizará o valor, mesmo quando o valor tiver sido alterado no repositório de configuração. O tempo de expiração padrão para cada solicitação é de 30 segundos, mas pode ser substituído se necessário.

Este tutorial mostra como você pode implementar atualizações de configuração dinâmicas em seu código. Ele se baseia no aplicativo introduzido nos guias de início rápido. Antes de continuar, conclua [a criação de um aplicativo .NET Framework com a configuração de aplicativo](./quickstart-dotnet-app.md) primeiro.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure seu aplicativo .NET Framework para atualizar sua configuração em resposta a alterações em um repositório de configuração de aplicativo.
> * Insira a configuração mais recente em seu aplicativo.
## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 ou posterior](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Criar um repositório de configurações de aplicativo

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Configuration Explorer** >  **+ criar** para adicionar os seguintes pares de chave-valor:

    | Chave | Valor |
    |---|---|
    | TestApp: configurações: mensagem | Dados da configuração Azure App |

    Deixe **rótulo** e **tipo de conteúdo** vazio por enquanto.

## <a name="create-a-net-framework-console-app"></a>Criar um aplicativo de console .NET Framework

1. Inicie o Visual Studio e selecione **arquivo** > **novo** **projeto**de > .

1. Em **criar um novo projeto**, filtre o tipo de projeto de **console** e clique em **aplicativo de console (.NET Framework)** . Clique em **Seguinte**.

1. Em **configurar seu novo projeto**, insira um nome de projeto. Em **estrutura**, selecione **.NET Framework 4.7.1** ou superior. Clique em **Criar**.

## <a name="reload-data-from-app-configuration"></a>Recarregar dados da configuração do aplicativo
1. Clique com o botão direito do mouse em seu projeto e selecione **gerenciar pacotes NuGet**. Na guia **procurar** , pesquise e adicione o pacote NuGet *Microsoft. Extensions. Configuration. AzureAppConfiguration* ao seu projeto. Se você não conseguir encontrá-lo, marque a caixa de seleção **incluir pré-lançamento** .

1. Abra *Program.cs*e adicione uma referência ao provedor de configuração do aplicativo .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Adicione duas variáveis para armazenar objetos relacionados à configuração.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Atualize o método `Main` para se conectar à configuração do aplicativo com as opções de atualização especificadas.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });

            _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }
    ```
    O método `ConfigureRefresh` é usado para especificar as configurações usadas para atualizar os dados de configuração com o repositório de configuração de aplicativo quando uma operação de atualização é disparada. Uma instância de `IConfigurationRefresher` pode ser recuperada chamando `GetRefresher` método nas opções fornecidas ao método `AddAzureAppConfiguration`, e o método `Refresh` nessa instância pode ser usado para disparar uma operação de atualização em qualquer lugar no seu código.

    > [!NOTE]
    > O tempo de expiração de cache padrão para uma definição de configuração é de 30 segundos, mas pode ser substituído chamando o método `SetCacheExpiration` no inicializador de opções passado como um argumento para o método `ConfigureRefresh`.

1. Adicione um método chamado `PrintMessage()` que dispara uma atualização manual dos dados de configuração da configuração do aplicativo.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.Refresh();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Defina uma variável de ambiente chamada **ConnectionString**e defina-a como a chave de acesso para seu repositório de configuração de aplicativo. Se você usar o prompt de comando do Windows, execute o seguinte comando e reinicie o prompt de comando para permitir que a alteração entre em vigor:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se você usar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Reinicie o Visual Studio para permitir que a alteração entre em vigor. 

1. Pressione CTRL + F5 para compilar e executar o aplicativo de console.

    ![Local de inicialização do aplicativo](./media/dotnet-app-run.png)

1. Iniciar sessão no [portal do Azure](https://portal.azure.com). Selecione **todos os recursos**e selecione a instância do repositório de configuração de aplicativo que você criou no guia de início rápido.

1. Selecione **Configuration Explorer**e atualize os valores das seguintes chaves:

    | Chave | Valor |
    |---|---|
    | TestApp: configurações: mensagem | Dados da configuração Azure App-atualizado |

1. De volta ao aplicativo em execução, pressione a tecla Enter para disparar uma atualização e imprimir o valor atualizado no prompt de comando ou na janela do PowerShell.

    ![Local de atualização do aplicativo](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Como o tempo de expiração do cache foi definido como 10 segundos usando o método `SetCacheExpiration` ao especificar a configuração para a operação de atualização, o valor da definição de configuração só será atualizado se pelo menos 10 segundos tiver decorrido desde a última atualização para essa configuração.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você habilitou seu aplicativo .NET Framework para atualizar dinamicamente as definições de configuração da configuração do aplicativo. Para saber como usar uma identidade gerenciada do Azure para simplificar o acesso à configuração do aplicativo, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerenciada](./howto-integrate-azure-managed-service-identity.md)
