---
title: 'Tutorial: Use configuração dinâmica numa aplicação .NET Core'
titleSuffix: Azure App Configuration
description: Neste tutorial, aprende-se a atualizar dinamicamente os dados de configuração para aplicações .NET Core
services: azure-app-configuration
documentationcenter: ''
author: abarora
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: abarora
ms.openlocfilehash: 95c48bded224a40879328dba5861b3564f0ebcf8
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979866"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Tutorial: Use configuração dinâmica numa aplicação .NET Core

A biblioteca de clientes de Configuração de Aplicação .NET Core suporta a atualização da configuração a pedido sem causar o reinício de uma aplicação. Isto pode ser implementado primeiro obtendo uma instância `IConfigurationRefresher` das opções para o fornecedor de configuração e, em seguida, invocando `TryRefreshAsync` esse caso em qualquer lugar do seu código.

Para manter as definições atualizadas e evitar demasiadas chamadas para a loja de configuração, é utilizado um cache para cada definição. Até que o valor em cache de uma definição tenha expirado, a operação de atualização não atualiza o valor, mesmo quando o valor foi alterado na loja de configuração. O tempo de validade padrão para cada pedido é de 30 segundos, mas pode ser ultrapassado se necessário.

Este tutorial mostra como pode implementar atualizações dinâmicas de configuração no seu código. Baseia-se na aplicação introduzida nos quickstarts. Antes de continuar, termine [Criar uma aplicação .NET Core com Configuração de Aplicação](./quickstart-dotnet-core-app.md) primeiro.

Você pode usar qualquer editor de código para fazer os passos neste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível nas plataformas Windows, macOS e Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie a sua aplicação .NET Core para atualizar a sua configuração em resposta a alterações numa loja de Configuração de Aplicações.
> * Consuma a mais recente configuração da sua aplicação.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este tutorial, instale o [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Recarregar dados da Configuração de Aplicações

Abra *Program.cs* e atualize o ficheiro para adicionar uma referência ao espaço de `System.Threading.Tasks` nomes, para especificar a configuração de atualização no `AddAzureAppConfiguration` método e para desencadear a atualização manual utilizando o `TryRefreshAsync` método.

```csharp
using System;
using System.Threading.Tasks;

namespace TestConsole
{
class Program
{
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;

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

    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.TryRefreshAsync();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
}
}
```

O `ConfigureRefresh` método é utilizado para especificar as definições utilizadas para atualizar os dados de configuração com a loja de Configuração de Aplicações quando uma operação de atualização é desencadeada. Um caso pode ser recuperado através do método de `IConfigurationRefresher` chamada `GetRefresher` sobre as opções fornecidas ao `AddAzureAppConfiguration` método, e o método neste `TryRefreshAsync` caso pode ser usado para desencadear uma operação de atualização em qualquer lugar do seu código.
    
> [!NOTE]
> O tempo de expiração da cache padrão para uma definição de configuração é de 30 segundos, mas pode ser ultrapassado chamando o `SetCacheExpiration` método sobre o inicializador de opções passado como um argumento para o `ConfigureRefresh` método.

## <a name="build-and-run-the-app-locally"></a>Construa e execute a app localmente

1. Desaprote uma variável ambiental chamada **ConnectionString** e desaprote-a na chave de acesso à sua loja de Configuração de Aplicações. Se utilizar o pedido de comando do Windows, executar o seguinte comando e reiniciar a solicitação de comando para permitir que a alteração entre em vigor:

    ```console
     setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o Windows PowerShell, executar o seguinte comando:

    ```powershell
     $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se utilizar o macOS ou o Linux, executar o seguinte comando:

    ```console
     export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Executar o seguinte comando para construir a aplicação de consola:

    ```console
     dotnet build
    ```

1. Após a construção concluída com sucesso, executar o seguinte comando para executar a aplicação localmente:

    ```console
     dotnet run
    ```

    ![Quickstart app lançar local](./media/quickstarts/dotnet-core-app-run.png)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Todos os recursos** e selecione a instância da loja de Configuração de Aplicações que criou no arranque rápido.

1. Selecione **Configuration Explorer** e atualize os valores das seguintes teclas:

    | Chave | Valor |
    |---|---|
    | TestApp:Definições:Mensagem | Dados da Configuração da Aplicação Azure - Atualizado |

1. Prima a tecla 'Introduzir' para ativar uma atualização e imprimir o valor atualizado na janela 'Solicitação de Comando' ou PowerShell.

    ![App Quickstart refresca local](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Uma vez que o tempo de validade da cache foi definido para 10 segundos usando o `SetCacheExpiration` método enquanto especifica a configuração para a operação de atualização, o valor para a definição de configuração só será atualizado se pelo menos 10 segundos decorridos desde a última atualização para essa definição.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, permitiu que a sua aplicação .NET Core atualizasse dinamicamente as definições de configuração a partir da Configuração da Aplicação. Para aprender a usar uma identidade gerida pelo Azure para agilizar o acesso à Configuração de Aplicações, continue até ao próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./howto-integrate-azure-managed-service-identity.md)
