---
title: '.NET Framework Tutorial: configuração dinâmica na Configuração da Aplicação Azure'
description: Neste tutorial, aprende-se a atualizar dinamicamente os dados de configuração para aplicações .NET Framework utilizando a Configuração da Aplicação Azure.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/24/2020
ms.author: alkemper
ms.openlocfilehash: 03940a86176d0bc93c5066977fdc87de5c456060
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932766"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Tutorial: Use configuração dinâmica numa aplicação .NET Framework

A biblioteca de clientes de Configuração de Aplicação .NET suporta a atualização de um conjunto de configurações a pedido sem causar o reinício de uma aplicação. Isto pode ser implementado primeiro obtendo uma instância `IConfigurationRefresher` das opções para o fornecedor de configuração e, em seguida, invocando `TryRefreshAsync` esse caso em qualquer lugar do seu código.

Para manter as definições atualizadas e evitar demasiadas chamadas para a loja de configuração, é utilizado um cache para cada definição. Até que o valor em cache de uma definição tenha expirado, a operação de atualização não atualiza o valor, mesmo quando o valor foi alterado na loja de configuração. O tempo de validade padrão para cada pedido é de 30 segundos, mas pode ser ultrapassado se necessário.

Este tutorial mostra como pode implementar atualizações dinâmicas de configuração no seu código. Baseia-se na aplicação introduzida nos quickstarts. Antes de continuar, termine [Criar uma aplicação .NET Framework com Configuração de Aplicações](./quickstart-dotnet-app.md) primeiro.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie a sua aplicação .NET Framework para atualizar a sua configuração em resposta a alterações numa loja de Configuração de Aplicações.
> * Injete a configuração mais recente na sua aplicação.
## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Quadro 4.7.1 ou posterior](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecione **Configuration Explorer**  >  **+ Crie** valor  >  **chave** para adicionar os seguintes pares de valor-chave:

    | Chave | Valor |
    |---|---|
    | TestApp:Definições:Mensagem | Dados da Configuração da Aplicação Azure |

    Deixe **a etiqueta** e o tipo de **conteúdo** vazios por enquanto.

8. Selecione **Aplicar**.

## <a name="create-a-net-framework-console-app"></a>Criar uma aplicação de consola.NET Framework

1. Inicie o Estúdio Visual e selecione **File**  >  **New**  >  **Project**.

1. In **Create a new project**, filter on the **Console** project type and click on Console **App (.NET Framework)**. Clique em **Seguinte**.

1. Em **Configurar o seu novo projeto,** insira um nome de projeto. No **quadro,** selecione **o quadro .NET 4.7.1** ou superior. Clique em **Criar**.

## <a name="reload-data-from-app-configuration"></a>Recarregar dados da Configuração de Aplicações
1. Clique com o botão direito no seu projeto e **selecione Gerir Pacotes NuGet**. No separador **Procurar,** procure e adicione a *Microsoft.Extensions.Configuration. Pacote NuGet AzureAppConfiguration* para o seu projeto. Se não conseguir encontrá-lo, selecione a caixa de verificação **de pré-escoam.**

1. Abra *Program.cs*, e adicione uma referência ao fornecedor de Configuração de Aplicação .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Adicione duas variáveis para armazenar objetos relacionados com a configuração.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Atualize o `Main` método para ligar à Configuração da Aplicação com as opções de atualização especificadas.

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
    O `ConfigureRefresh` método é utilizado para especificar as definições utilizadas para atualizar os dados de configuração com a loja de Configuração de Aplicações quando uma operação de atualização é desencadeada. Um caso pode ser recuperado através do `IConfigurationRefresher` método de chamada sobre as `GetRefresher` opções fornecidas ao `AddAzureAppConfiguration` método, e o método neste caso `TryRefreshAsync` pode ser usado para desencadear uma operação de atualização em qualquer lugar do seu código.

    > [!NOTE]
    > O tempo de expiração da cache padrão para uma definição de configuração é de 30 segundos, mas pode ser ultrapassado chamando o `SetCacheExpiration` método sobre o inicializador de opções passado como um argumento para o `ConfigureRefresh` método.

1. Adicione um método chamado `PrintMessage()` que desencadeia uma atualização manual dos dados de configuração da Configuração da Aplicação.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.TryRefreshAsync();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Construa e execute a app localmente

1. Desaprote uma variável ambiental chamada **ConnectionString** e desaprote-a na chave de acesso à sua loja de Configuração de Aplicações. Se utilizar o pedido de comando do Windows, executar o seguinte comando e reiniciar a solicitação de comando para permitir que a alteração entre em vigor:

    ```console
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o Windows PowerShell, executar o seguinte comando:

    ```powershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. Reinicie o Estúdio Visual para permitir que a alteração entre em vigor. 

1. Prima Ctrl + F5 para construir e executar a aplicação da consola.

    ![App lançar local](./media/dotnet-app-run.png)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Todos os recursos** e selecione a instância da loja de Configuração de Aplicações que criou no arranque rápido.

1. Selecione **Configuration Explorer** e atualize os valores das seguintes teclas:

    | Chave | Valor |
    |---|---|
    | TestApp:Definições:Mensagem | Dados da Configuração da Aplicação Azure - Atualizado |

1. Voltando à aplicação de execução, prima a tecla 'Introduzir' para ativar uma atualização e imprimir o valor atualizado na janela 'Solicitação de Comando' ou PowerShell.

    ![App atualizar local](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Uma vez que o tempo de validade da cache foi definido para 10 segundos usando o `SetCacheExpiration` método enquanto especifica a configuração para a operação de atualização, o valor para a definição de configuração só será atualizado se pelo menos 10 segundos decorridos desde a última atualização para essa definição.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, permitiu que a sua aplicação .NET Framework atualizasse dinamicamente as definições de configuração a partir da Configuração da Aplicação. Para aprender a usar uma identidade gerida pelo Azure para agilizar o acesso à Configuração de Aplicações, continue até ao próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./howto-integrate-azure-managed-service-identity.md)
