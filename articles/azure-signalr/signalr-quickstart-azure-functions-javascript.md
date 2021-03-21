---
title: Use o JavaScript para criar uma sala de chat com funções Azure e serviço SignalR
description: Um quickstart para usar o Serviço Azure SignalR e funções Azure para criar uma sala de chat usando JavaScript.
author: sffamily
ms.service: signalr
ms.devlang: javascript
ms.topic: quickstart
ms.date: 12/14/2019
ms.author: zhshang
ms.custom: devx-track-js
ms.openlocfilehash: 061dce01d2437d04d371ac65c115a1d95136fb5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94874700"
---
# <a name="quickstart-use-javascript-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Quickstart: Use o JavaScript para criar uma sala de chat com funções Azure e serviço SignalR

O Serviço Azure SignalR permite-lhe adicionar facilmente funcionalidades em tempo real à sua aplicação e o Azure Functions é uma plataforma sem servidor que lhe permite executar o seu código sem gerir qualquer infraestrutura. Neste arranque rápido, utiliza o JavaScript para construir uma aplicação de chat sem servidor em tempo real utilizando o Serviço e Funções SignalR.

## <a name="prerequisites"></a>Pré-requisitos

- Um editor de código, como [Visual Studio Code](https://code.visualstudio.com/)
- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing), versão 2 ou superior. Costumava executar aplicativos Azure Function localmente.
- [Node.js](https://nodejs.org/en/download/), versão 10.x

   > [!NOTE]
   > Os exemplos devem funcionar com outras versões de Node.js, ver documentação de [versões de execução do Azure Functions](../azure-functions/functions-versions.md#languages) para obter mais informações.

> [!NOTE]
> Este início rápido pode ser executado no macOS, Windows ou Linux.

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsjs)

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em <https://portal.azure.com/> com a sua conta do Azure.

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsjs)

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsjs)

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsjs)

## <a name="configure-and-run-the-azure-function-app"></a>Configurar e executar a aplicação Funções do Azure

1. No browser em que abriu o portal do Azure, certifique-se de que a instância Serviço SignalR implementada anteriormente foi totalmente criada com êxito ao pesquisar o nome na caixa de pesquisa na parte superior do portal. Selecione a instância para abri-la.

    ![Pesquisar a instância do Serviço SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Selecione **Chaves** para ver as cadeias de ligação para a instância do Serviço SignalR.

1. Selecione e copie a cadeia de ligação principal.

    ![Screenshot que realça a cadeia de ligação primária.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. No seu editor de código, abra a pasta *src/chat/javascript* no repositório clonado.

1. Mude o nome de *local.settings.sample.json* para *local.settings.json*.

1. Em **local.settings.json**, cole a cadeia de ligação no valor da definição **AzureSignalRConnectionString**. Guarde o ficheiro.

1. As funções de JavaScript são organizadas em pastas. Cada pasta tem dois ficheiros: *function.json* define os enlaces utilizados na função e *index.js* é o corpo da função. Existem duas funções acionadas por HTTP nesta aplicação de funções:

    - **negociar** - Utiliza o enlace de entrada *SignalRConnectionInfo* para gerar e devolver informações de ligação válidas.
    - **mensagens** - Recebe uma mensagem de chat no corpo do pedido e utiliza o enlace de saída *SignalR* para difundir a mensagem a todas as aplicações cliente ligadas.

1. No terminal, certifique-se de que está na pasta *src/chat/javascript.* Execute a aplicação de funções.

    ```bash
    func start
    ```

    ![Criar o Serviço SignalR](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-run-application.png)
    
Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsjs)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsjs)

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsjs)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, você construiu e executou uma aplicação sem servidor em tempo real no Código VS. Em seguida, saiba mais sobre como implementar as Funções do Azure a partir do VS Code.

> [!div class="nextstepaction"]
> [Implementar as Funções do Azure com o VS Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01)

