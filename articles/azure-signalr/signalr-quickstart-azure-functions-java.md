---
title: Use Java para criar uma sala de chat com funções Azure e serviço SignalR
description: Um quickstart para usar o Serviço Azure SignalR e funções Azure para criar uma sala de chat usando Java.
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.custom: devx-track-java
ms.openlocfilehash: 62f31d8426e86cc20e957f3a47b30109dee0a634
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185679"
---
# <a name="quickstart-use-java-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Quickstart: Use Java para criar uma sala de chat com funções Azure e serviço SignalR

O Serviço Azure SignalR permite-lhe adicionar facilmente funcionalidades em tempo real à sua aplicação e o Azure Functions é uma plataforma sem servidor que lhe permite executar o seu código sem gerir qualquer infraestrutura. Neste arranque rápido, você usa Java para construir uma aplicação de chat sem servidor em tempo real usando o Serviço e Funções SignalR.

## <a name="prerequisites"></a>Pré-requisitos

- Um editor de código, como [Visual Studio Code](https://code.visualstudio.com/)
- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Azure Funções Core Tools](https://github.com/Azure/azure-functions-core-tools#installing). Costumava executar aplicativos Azure Function localmente.

   > [!NOTE]
   > As ligações necessárias para o Serviço SignalR em Java só são suportadas na versão 2.4.419 (versão de anfitrião 2.0.12332) ou superior.

   > [!NOTE]
   > Para instalar extensões, as Ferramentas Principais de Funções Azure requerem a instalação [do SDK .NET Core.](https://www.microsoft.com/net/download) No entanto, não precisa de estar familiarizado com o .NET para criar aplicações das Funções do Azure com JavaScript.

- [Java Development Kit](https://www.azul.com/downloads/zulu/), versão 8
- [Apache Maven](https://maven.apache.org), versão 3.0 ou superior

> [!NOTE]
> Este início rápido pode ser executado no macOS, Windows ou Linux.

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsjava)

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em <https://portal.azure.com/> com a sua conta do Azure.

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsjava)

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsjava)

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsjava)

## <a name="configure-and-run-the-azure-function-app"></a>Configurar e executar a aplicação Funções do Azure

1. No browser em que abriu o portal do Azure, certifique-se de que a instância Serviço SignalR implementada anteriormente foi totalmente criada com êxito ao pesquisar o nome na caixa de pesquisa na parte superior do portal. Selecione a instância para abri-la.

    ![Pesquisar a instância do Serviço SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Selecione **Chaves** para ver as cadeias de ligação para a instância do Serviço SignalR.

1. Selecione e copie a cadeia de ligação principal.

    ![Criar o Serviço SignalR](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. No seu editor de código, abra a pasta *src/chat/java* no repositório clonado.

1. Mude o nome de *local.settings.sample.json* para *local.settings.json*.

1. Em **local.settings.json**, cole a cadeia de ligação no valor da definição **AzureSignalRConnectionString**. Guarde o ficheiro.

1. O ficheiro principal que contém as funções está em *src/chat/java/src/main/java/com/function/Funções.java*:

    - **negociar** - Utiliza o enlace de entrada *SignalRConnectionInfo* para gerar e devolver informações de ligação válidas.
    - **sendMessage** - Recebe uma mensagem de chat no organismo de pedido e utiliza a ligação de saída *SignalR* para transmitir a mensagem a todas as aplicações de clientes conectadas.

1. No terminal, certifique-se de que está na pasta *src/chat/java.* Construa o aplicativo de função.

    ```bash
    mvn clean package
    ```

1. Executar a aplicação de função localmente.

    ```bash
    mvn azure-functions:run
    ```
    
Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsjava)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsjava)

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qsjava)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, construíste e executaste uma aplicação sem servidor em tempo real usando o Maven. Em seguida, aprenda sobre como criar Java Azure Functions do zero.

> [!div class="nextstepaction"]
> [Crie a sua primeira função com Java e Maven](../azure-functions/create-first-function-cli-csharp.md?pivots=programming-language-java%2cprogramming-language-java)