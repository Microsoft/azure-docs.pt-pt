---
title: Tutorial para utilizar a configuração dinâmica de configuração de aplicações do Azure numa aplicação .NET Core | Documentos da Microsoft
description: Neste tutorial, irá aprender a atualizar dinamicamente os dados de configuração para aplicações de .NET Core
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
ms.openlocfilehash: 1649fefda5073761d616fc48c602cab84d293ed0
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799974"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Tutorial: Utilizar a configuração dinâmica de uma aplicação .NET Core

A biblioteca de cliente de configuração de aplicações .NET Core oferece suporte a atualizar um conjunto de definições de configuração a pedido sem fazer com que um aplicativo reinicie. Isso pode ser implementado, primeiro, obtendo uma instância do `IConfigurationRefresher` entre as opções para o fornecedor de configuração e, em seguida, chamar `Refresh` nessa instância em qualquer lugar no seu código.

Para manter as definições atualizadas e evitar demasiadas chamadas para o armazenamento de configuração, um cache é utilizada para cada definição. Até que o valor em cache de uma configuração tiver expirado, a operação de atualização não atualiza o valor, mesmo quando o valor foi alterado no arquivo de configuração. A hora de expiração predefinida para cada solicitação é 30 segundos, mas pode ser substituído se necessário.

Este tutorial mostra como pode implementar atualizações de configuração dinâmica no seu código. Ele se baseia na aplicação introduzida nos rápidos. Antes de continuar, concluir [criar uma aplicação .NET Core com a configuração de aplicações](./quickstart-dotnet-core-app.md) primeiro.

Pode utilizar qualquer editor de código para realizar os passos neste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção está disponível no Windows, macOS e plataformas Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configure a aplicação para atualizar a respetiva configuração com um arquivo de configuração de aplicação a pedido.
> * Inserir a configuração mais recente em controladores de seu aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

Para fazer este tutorial, instale o [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Recarregar os dados de configuração de aplicações

Open *Program.cs* e atualizar o ficheiro para especificar a configuração de atualização no `AddAzureAppConfiguration` método e usando atualização manual de Acionador a `Refresh` método.

```csharp
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
```

O `ConfigureRefresh` método é usado para especificar as definições utilizadas para atualizar os dados de configuração com o armazenamento de configuração de aplicação quando uma operação de atualização é acionada. Uma instância do `IConfigurationRefresher` pode ser obtido chamando `GetRefresher` método sobre as opções fornecidas para `AddAzureAppConfiguration` método e o `Refresh` método nesta instância pode ser utilizado para acionar uma operação de atualização em qualquer lugar no seu código.
    
> [!NOTE]
> A hora de expiração do cache padrão para uma definição de configuração é de 30 segundos, mas pode ser substituída ao chamar o `SetCacheExpiration` método no inicializador de opções passada como um argumento para o `ConfigureRefresh` método.

## <a name="build-and-run-the-app-locally"></a>Criar e executar a aplicação localmente

1. Definir uma variável de ambiente com o nome **ConnectionString**e defina-o para a chave de acesso ao seu arquivo de configuração de aplicação. Se utilizar a linha de comandos do Windows, execute o seguinte comando e reinicie o prompt de comando para permitir que a alteração tenha efeito:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se utilizar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Se usar o macOS ou Linux, execute o seguinte comando:

        export ConnectionString='connection-string-of-your-app-configuration-store'

1. Execute o seguinte comando para criar a aplicação de consola:

        dotnet build

1. Após a compilação for concluída com êxito, execute o seguinte comando para executar a aplicação localmente:

        dotnet run

    ![Início rápido iniciação da aplicação local](./media/quickstarts/dotnet-core-app-run.png)

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **todos os recursos**e selecione a instância de arquivo de configuração de aplicação que criou no guia de introdução.

1. Selecione **Explorador de configuração**e atualize os valores das seguintes chaves:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:Message | Dados de configuração de aplicações do Azure - atualizada |

1. Prima a tecla Enter para acionar uma atualização e imprimir o valor atualizado na janela de linha de comandos ou PowerShell.

    ![Atualização de aplicação de início rápido local](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Uma vez que a hora de expiração do cache foi definida para 10 segundos usando o `SetCacheExpiration` método ao especificar a configuração para a operação de atualização, o valor para a definição de configuração apenas será atualizado se, pelo menos, 10 segundos decorridos desde a última atualização para essa definição.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, adicionou do Azure para simplificar o acesso à configuração de aplicações e melhorar a gestão de credenciais para a sua aplicação, a identidade de serviço gerido. Para saber mais sobre como utilizar a configuração de aplicações, avance para os exemplos da CLI do Azure.

> [!div class="nextstepaction"]
> [Amostras de CLI](./cli-samples.md)
