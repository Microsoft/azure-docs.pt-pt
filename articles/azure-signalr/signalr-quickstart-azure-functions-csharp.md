---
title: Guia de introdução de sem servidor SignalR serviço do Azure-C#
description: Um início rápido para utilizar o Serviço Azure SignalR e as Funções do Azure para criar uma sala de chat.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.openlocfilehash: e3a437d44c557d2ec53182d4f6178cbea3e54eba
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544985"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-c"></a>Início rápido: Criar uma sala de bate-papo com as funções do Azure e o serviço SignalR com C\#

O Serviço Azure SignalR permite-lhe adicionar facilmente funcionalidades em tempo real à sua aplicação. As Funções do Azure são uma plataforma sem servidor que lhe permite executar código sem ter de gerir qualquer infraestrutura. Neste início rápido, saiba como utilizar o Serviço SignalR e as Funções para criar uma aplicação de chat em tempo real sem servidor.

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não tiver o Visual Studio 2017 instalado, pode transferir e utilizar a [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **gratuita**. Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.

Também pode executar este tutorial na linha de comando (macOS, Windows ou Linux) com o [ferramentas de núcleo de funções do Azure (v2)](https://github.com/Azure/azure-functions-core-tools#installing), o [SDK para .NET Core](https://dotnet.microsoft.com/download)e seu editor de código preferido.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em <https://portal.azure.com/> com a sua conta do Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>Configurar e executar a aplicação Funções do Azure

1. Inicie o Visual Studio (ou outro editor de código) e abra a solução no *chat\src\csharp* pasta do repositório clonado.

1. No browser em que abriu o portal do Azure, certifique-se de que a instância Serviço SignalR implementada anteriormente foi totalmente criada com êxito ao pesquisar o nome na caixa de pesquisa na parte superior do portal. Selecione a instância para abri-la.

    ![Pesquisar a instância do Serviço SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Selecione **Chaves** para ver as cadeias de ligação para a instância do Serviço SignalR.

1. Selecione e copie a cadeia de ligação principal.

1. Novamente no Visual Studio, no Explorador de Soluções, renomeie *local.settings.sample.json* para *local.settings.json*.

1. Em **local.settings.json**, cole a cadeia de ligação no valor da definição **AzureSignalRConnectionString**. Guarde o ficheiro.

1. Abra **Functions.cs**. Existem duas funções acionadas por HTTP nesta aplicação de funções:

    - **GetSignalRInfo** - Utiliza o enlace de entrada *SignalRConnectionInfo* para gerar e devolver informações de ligação válidas.
    - **SendMessage** - Recebe uma mensagem de chat no corpo do pedido e utiliza o enlace de saída *SignalR* para difundir a mensagem a todas as aplicações cliente ligadas.

1. Utilize uma das seguintes opções para iniciar a aplicação de função do Azure localmente.

    - **Visual Studio**: No menu *Depurar*, selecione *Iniciar depuração* para executar a aplicação.

        ![Depurar a aplicação](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-debug-vs.png)

    - **Linha de comandos**: Execute o seguinte comando para iniciar o host de função.

        ```bash
        func start
        ```

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou e executou uma aplicação sem servidor em tempo real no Visual Studio. Em seguida, saiba mais sobre como desenvolver e implementar as Funções do Azure com o Visual Studio.

> [!div class="nextstepaction"]
> [Desenvolver as Funções do Azure com o Visual Studio](../azure-functions/functions-develop-vs.md)