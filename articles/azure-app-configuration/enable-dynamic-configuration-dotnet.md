---
title: '.NET Framework Tutorial: configuração dinâmica na configuração da app Azure'
description: Neste tutorial, aprende-se a atualizar de forma dinâmica os dados de configuração para aplicações .NET Framework utilizando a Configuração de Aplicações Azure.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 7780bdbc92868f62e8d066d171b2a04fe06a981d
ms.sourcegitcommit: 940e16ff194d5163f277f98d038833b1055a1a3e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2020
ms.locfileid: "80245808"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Tutorial: Utilize a configuração dinâmica numa aplicação .NET Framework

A biblioteca de clientes da App Configuration .NET suporta atualizar um conjunto de configurações de configuração a pedido sem causar o reinício de uma aplicação. Isto pode ser implementado primeiro obtendo uma instância das `IConfigurationRefresher` opções `Refresh` para o fornecedor de configuração e, em seguida, chamando esse caso em qualquer lugar do seu código.

Para manter as definições atualizadas e evitar demasiadas chamadas para a loja de configuração, é utilizada uma cache para cada definição. Até que o valor cached de uma definição tenha expirado, a operação de atualização não atualiza o valor, mesmo quando o valor mudou na loja de configuração. O tempo de validade padrão para cada pedido é de 30 segundos, mas pode ser ultrapassado se necessário.

Este tutorial mostra como pode implementar atualizações dinâmicas de configuração no seu código. Baseia-se na aplicação introduzida nos quickstarts. Antes de continuar, termine [Criar uma aplicação .NET Framework com configuração](./quickstart-dotnet-app.md) de aplicações primeiro.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie a sua aplicação .NET Framework para atualizar a sua configuração em resposta a alterações numa loja de Configuração de Aplicações.
> * Injete a última configuração na sua aplicação.
## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.QUADRO LÍQUIDO 4.7.1 ou posterior](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicações

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Select Configuration Explorer** > **+ Criar** > o**valor-chave** para adicionar os seguintes pares de valor-chave:

    | Chave | Valor |
    |---|---|
    | TestApp:Definições:Mensagem | Dados da Configuração de Aplicações Azure |

    Deixe o **rótulo** e o **tipo de conteúdo** vazios por enquanto.

7. Selecione **Aplicar**.

## <a name="create-a-net-framework-console-app"></a>Criar uma aplicação de consola .NET Framework

1. Inicie o Estúdio Visual e selecione **File** > **New** > **Project**.

1. Em **Criar um novo projeto,** filtre no tipo de projeto da **Consola** e clique na App **consola (.NET Framework)**. Clique em **Seguinte**.

1. Em **Configure o seu novo projeto,** insira um nome de projeto. **No quadro**, selecione **.NET Quadro 4.7.1** ou superior. Clique em **Criar**.

## <a name="reload-data-from-app-configuration"></a>Recarregar dados da configuração da app
1. Clique no seu projeto e selecione **Gerir pacotes NuGet**. No separador **Browse,** procure e adicione o pacote *NuGet microsoft.Extensions.Configuration.AzureAppConfiguration* NuGet ao seu projeto. Se não conseguir encontrá-la, selecione a caixa de verificação **de pré-lançamento Incluir.**

1. Abra *Program.cs*e adicione uma referência ao fornecedor de configuração de aplicações .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Adicione duas variáveis para armazenar objetos relacionados com a configuração.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Atualize `Main` o método para se ligar à Configuração da Aplicação com as opções de atualização especificadas.

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
    O `ConfigureRefresh` método é utilizado para especificar as definições utilizadas para atualizar os dados de configuração com a loja de configuração da aplicação quando é acionada uma operação de atualização. Uma instância `IConfigurationRefresher` pode ser recuperada através do método de chamada `GetRefresher` sobre as opções fornecidas ao `AddAzureAppConfiguration` método, e o `Refresh` método neste caso pode ser usado para desencadear uma operação de atualização em qualquer lugar do seu código.

    > [!NOTE]
    > O tempo de validade da cache padrão para uma definição de configuração é de 30 segundos, mas pode ser ultrapassado chamando o `SetCacheExpiration` método sobre as opções inicializador espádacomo argumento para o `ConfigureRefresh` método.

1. Adicione um `PrintMessage()` método chamado que desencadeie uma atualização manual dos dados de configuração a partir da Configuração da Aplicação.

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

## <a name="build-and-run-the-app-locally"></a>Construir e executar a app localmente

1. Detete uma variável ambiental chamada **ConnectionString**e detetete-a na chave de acesso à sua loja de configuração de aplicações. Se utilizar o pedido de comando do Windows, execute o seguinte comando e reinicie a solicitação de comando para permitir que a alteração faça efeito:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se utilizar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Reiniciar o Estúdio Visual para permitir que a alteração faça efeito. 

1. Prima Ctrl + F5 para construir e executar a aplicação de consola.

    ![App lança local](./media/dotnet-app-run.png)

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Todos os recursos**e selecione a instância da loja de configuração de aplicações que criou no arranque rápido.

1. Selecione O Explorador de **Configuração**e atualize os valores das seguintes teclas:

    | Chave | Valor |
    |---|---|
    | TestApp:Definições:Mensagem | Dados da Configuração de Aplicações Azure - Atualizado |

1. De volta à aplicação de execução, prima a tecla Enter para desencadear uma atualização e imprima o valor atualizado na janela Command Prompt ou PowerShell.

    ![App refrescar local](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Uma vez que o tempo de validade `SetCacheExpiration` da cache foi definido para 10 segundos utilizando o método, especificando a configuração para o funcionamento de atualização, o valor para a configuração da configuração só será atualizado se decorridos pelo menos 10 segundos desde a última atualização para essa definição.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ativou a sua aplicação .NET Framework para atualizar dinamicamente as definições de configuração a partir da Configuração da Aplicação. Para aprender a usar uma identidade gerida pelo Azure para agilizar o acesso à Configuração de Aplicações, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./howto-integrate-azure-managed-service-identity.md)
