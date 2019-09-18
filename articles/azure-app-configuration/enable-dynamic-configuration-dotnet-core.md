---
title: Tutorial para usar a configuração dinâmica de configuração de Azure App em um aplicativo .NET Core | Microsoft Docs
description: Neste tutorial, você aprenderá a atualizar dinamicamente os dados de configuração para aplicativos .NET Core
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
ms.openlocfilehash: 3eee34f594cb23a8b64f6fd10837c9a641eda62d
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71075978"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Tutorial: Usar a configuração dinâmica em um aplicativo .NET Core

A biblioteca de cliente .NET Core de configuração de aplicativo dá suporte à atualização de um conjunto de definições de configuração sob demanda sem causar a reinicialização de um aplicativo. Isso pode ser implementado pela primeira vez que você obtém `IConfigurationRefresher` uma instância do das opções do provedor de configuração e `Refresh` , em seguida, chamando essa instância em qualquer lugar no seu código.

Para manter as configurações atualizadas e evitar muitas chamadas para o repositório de configuração, um cache é usado para cada configuração. Até que o valor em cache de uma configuração tenha expirado, a operação de atualização não atualizará o valor, mesmo quando o valor tiver sido alterado no repositório de configuração. O tempo de expiração padrão para cada solicitação é de 30 segundos, mas pode ser substituído se necessário.

Este tutorial mostra como você pode implementar atualizações de configuração dinâmicas em seu código. Ele se baseia no aplicativo introduzido nos guias de início rápido. Antes de continuar, conclua [a criação de um aplicativo .NET Core com a configuração de aplicativo](./quickstart-dotnet-core-app.md) primeiro.

Você pode usar qualquer editor de código para executar as etapas neste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção disponível nas plataformas Windows, MacOS e Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure seu aplicativo para atualizar sua configuração com um repositório de configuração de aplicativo sob demanda.
> * Insira a configuração mais recente nos controladores do aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este tutorial, instale o [SDK do .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Recarregar dados da configuração do aplicativo

Abra *Program.cs* e atualize o arquivo para adicionar uma referência ao `System.Threading.Tasks` namespace, para especificar a configuração de `AddAzureAppConfiguration` atualização no método e para disparar a atualização manual usando o `Refresh` método.

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
        IConfigurationRefresher refresher = null;

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

O `ConfigureRefresh` método é usado para especificar as configurações usadas para atualizar os dados de configuração com o repositório de configuração de aplicativo quando uma operação de atualização é disparada. Uma instância de `IConfigurationRefresher` pode ser recuperada por `GetRefresher` meio da chamada de método nas `AddAzureAppConfiguration` opções fornecidas ao método `Refresh` , e o método nessa instância pode ser usado para disparar uma operação de atualização em qualquer lugar no seu código.
    
> [!NOTE]
> O tempo de expiração de cache padrão para uma definição de configuração é de 30 segundos, mas pode `SetCacheExpiration` ser substituído chamando o método no inicializador de opções passado `ConfigureRefresh` como um argumento para o método.

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Defina uma variável de ambiente chamada **ConnectionString**e defina-a como a chave de acesso para seu repositório de configuração de aplicativo. Se você usar o prompt de comando do Windows, execute o seguinte comando e reinicie o prompt de comando para permitir que a alteração entre em vigor:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se você usar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se você usar o macOS ou Linux, execute o seguinte comando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

1. Execute o seguinte comando para compilar o aplicativo de console:

        dotnet build

1. Depois que a compilação for concluída com êxito, execute o seguinte comando para executar o aplicativo localmente:

        dotnet run

    ![Local de inicialização do aplicativo de início rápido](./media/quickstarts/dotnet-core-app-run.png)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **todos os recursos**e selecione a instância do repositório de configuração de aplicativo que você criou no guia de início rápido.

1. Selecione **Configuration Explorer**e atualize os valores das seguintes chaves:

    | Chave | Value |
    |---|---|
    | TestApp: configurações: mensagem | Dados da configuração Azure App-atualizado |

1. Pressione a tecla Enter para disparar uma atualização e imprimir o valor atualizado no prompt de comando ou na janela do PowerShell.

    ![Local de atualização do aplicativo de início rápido](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Como o tempo de expiração do cache foi definido como 10 `SetCacheExpiration` segundos usando o método ao especificar a configuração para a operação de atualização, o valor da definição de configuração só será atualizado se pelo menos 10 segundos tiver decorrido desde a última atualização para essa configuração.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você adicionou uma identidade de serviço gerenciado do Azure para simplificar o acesso à configuração de aplicativo e melhorar o gerenciamento de credenciais para seu aplicativo. Para saber mais sobre como usar a configuração de aplicativo, prossiga para os exemplos de CLI do Azure.

> [!div class="nextstepaction"]
> [Amostras de CLI](./cli-samples.md)
