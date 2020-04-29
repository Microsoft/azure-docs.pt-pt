---
title: 'Tutorial: Use a configuração dinâmica numa aplicação .NET Core'
titleSuffix: Azure App Configuration
description: Neste tutorial, aprende-se a atualizar de forma dinâmica os dados de configuração para aplicações .NET Core
services: azure-app-configuration
documentationcenter: ''
author: abarora
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: abarora
ms.openlocfilehash: afecc84748ae8ce85c07e3b482bd9b596bdca251
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75433669"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Tutorial: Use a configuração dinâmica numa aplicação .NET Core

A biblioteca de clientes da App Configuration .NET Core suporta atualizar um conjunto de configurações de configuração a pedido sem causar o reinício de uma aplicação. Isto pode ser implementado primeiro obtendo uma instância das `IConfigurationRefresher` opções `Refresh` para o fornecedor de configuração e, em seguida, chamando esse caso em qualquer lugar do seu código.

Para manter as definições atualizadas e evitar demasiadas chamadas para a loja de configuração, é utilizada uma cache para cada definição. Até que o valor cached de uma definição tenha expirado, a operação de atualização não atualiza o valor, mesmo quando o valor mudou na loja de configuração. O tempo de validade padrão para cada pedido é de 30 segundos, mas pode ser ultrapassado se necessário.

Este tutorial mostra como pode implementar atualizações dinâmicas de configuração no seu código. Baseia-se na aplicação introduzida nos quickstarts. Antes de continuar, termine [Criar uma aplicação .NET Core com configuração](./quickstart-dotnet-core-app.md) de aplicações primeiro.

Pode usar qualquer editor de código para fazer os passos neste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção que está disponível nas plataformas Windows, macOS e Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar a sua aplicação .NET Core para atualizar a sua configuração em resposta a alterações numa loja de Configuração de Aplicações.
> * Consuma a configuração mais recente da sua aplicação.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este tutorial, instale o [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Recarregar dados da configuração da app

Abra *Program.cs* e atualize o ficheiro `System.Threading.Tasks` para adicionar uma referência `AddAzureAppConfiguration` ao espaço de nome, `Refresh` para especificar a configuração de atualização no método e para desencadear a atualização manual utilizando o método.

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

        await _refresher.Refresh();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
}
}
```

O `ConfigureRefresh` método é utilizado para especificar as definições utilizadas para atualizar os dados de configuração com a loja de configuração da aplicação quando é acionada uma operação de atualização. Uma instância `IConfigurationRefresher` pode ser recuperada através do método de chamada `GetRefresher` sobre as opções fornecidas ao `AddAzureAppConfiguration` método, e o `Refresh` método neste caso poderia ser usado para desencadear uma operação de atualização em qualquer lugar do seu código.
    
> [!NOTE]
> O tempo de validade da cache padrão para uma definição de configuração é de 30 segundos, mas pode ser ultrapassado chamando o `SetCacheExpiration` método sobre as opções inicializador espádacomo argumento para o `ConfigureRefresh` método.

## <a name="build-and-run-the-app-locally"></a>Construir e executar a app localmente

1. Detete uma variável ambiental chamada **ConnectionString**e detetete-a na chave de acesso à sua loja de configuração de aplicações. Se utilizar o pedido de comando do Windows, execute o seguinte comando e reinicie a solicitação de comando para permitir que a alteração faça efeito:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se utilizar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se utilizar macOS ou Linux, execute o seguinte comando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

1. Executar o seguinte comando para construir a aplicação de consola:

        dotnet build

1. Depois de a construção concluída com sucesso, execute o seguinte comando para executar a app localmente:

        dotnet run

    ![Lançamento de app Quickstart local](./media/quickstarts/dotnet-core-app-run.png)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Todos os recursos**e selecione a instância da loja de configuração de aplicações que criou no arranque rápido.

1. Selecione O Explorador de **Configuração**e atualize os valores das seguintes teclas:

    | Chave | Valor |
    |---|---|
    | TestApp:Definições:Mensagem | Dados da Configuração de Aplicações Azure - Atualizado |

1. Prima a tecla Enter para ativar uma atualização e imprimir o valor atualizado na janela Command Prompt ou PowerShell.

    ![App Quickstart refrescar local](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Uma vez que o tempo de validade `SetCacheExpiration` da cache foi definido para 10 segundos utilizando o método, especificando a configuração para o funcionamento de atualização, o valor para a configuração da configuração só será atualizado se decorridos pelo menos 10 segundos desde a última atualização para essa definição.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ativou a sua aplicação .NET Core para atualizar dinamicamente as definições de configuração a partir da Configuração da Aplicação. Para aprender a usar uma identidade gerida pelo Azure para agilizar o acesso à Configuração de Aplicações, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./howto-integrate-azure-managed-service-identity.md)
