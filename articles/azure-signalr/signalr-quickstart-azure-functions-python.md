---
title: Serviço De Sinalização Azure quickstart sem servidor - Python
description: Um início rápido para utilizar o Serviço Azure SignalR e as Funções do Azure para criar uma sala de chat.
author: anthonychu
ms.service: signalr
ms.devlang: python
ms.topic: quickstart
ms.date: 12/14/2019
ms.author: antchu
ms.openlocfilehash: 0cf8705cf2567a60129681c2db41b0868f8fe182
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "75392129"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-python"></a>Quickstart: Criar uma sala de chat com funções Azure e serviço de sinalização usando Python

O Serviço Azure SignalR permite-lhe adicionar facilmente funcionalidades em tempo real à sua aplicação. As Funções do Azure são uma plataforma sem servidor que lhe permite executar código sem ter de gerir qualquer infraestrutura. Neste início rápido, saiba como utilizar o Serviço SignalR e as Funções para criar uma aplicação de chat em tempo real sem servidor.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido pode ser executado no macOS, Windows ou Linux.

Certifique-se de que tem um editor de código como o [Visual Studio Code](https://code.visualstudio.com/) instalado.

Instale as [Ferramentas Core funções do Azure](https://github.com/Azure/azure-functions-core-tools#installing) (versão 2.7.1505 ou superior) para executar as aplicações Python Azure Function localmente.

Funções Azure requer [Python 3.6 ou 3.7](https://www.python.org/downloads/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em <https://portal.azure.com/> com a sua conta do Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>Configurar e executar a aplicação Funções do Azure

1. No browser em que abriu o portal do Azure, certifique-se de que a instância Serviço SignalR implementada anteriormente foi totalmente criada com êxito ao pesquisar o nome na caixa de pesquisa na parte superior do portal. Selecione a instância para abri-la.

    ![Pesquisar a instância do Serviço SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Selecione **Chaves** para ver as cadeias de ligação para a instância do Serviço SignalR.

1. Selecione e copie a cadeia de ligação principal.

    ![Criar o Serviço SignalR](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. No seu editor de código, abra a pasta *src/chat/python* no repositório clonado.

1. Para desenvolver e testar localmente as funções Python, deve trabalhar num ambiente Python 3.6 ou 3.7. Executar os seguintes comandos para criar `.venv`e ativar um ambiente virtual chamado .

    **Linux ou macOS:**

    ```bash
    python3.7 -m venv .venv
    source .venv/bin/activate
    ```

    **Janelas:**

    ```powershell
    py -3.7 -m venv .venv
    .venv\scripts\activate
    ```

1. Mude o nome de *local.settings.sample.json* para *local.settings.json*.

1. Em **local.settings.json**, cole a cadeia de ligação no valor da definição **AzureSignalRConnectionString**. Guarde o ficheiro.

1. As funções python são organizadas em pastas. Em cada pasta estão dois ficheiros: *function.json* define as encadernações que são usadas na função, e * \_ \_inite\_\_.py* é o corpo da função. Existem duas funções acionadas por HTTP nesta aplicação de funções:

    - **negociar** - Utiliza o enlace de entrada *SignalRConnectionInfo* para gerar e devolver informações de ligação válidas.
    - **mensagens** - Recebe uma mensagem de chat no corpo do pedido e utiliza o enlace de saída *SignalR* para difundir a mensagem a todas as aplicações cliente ligadas.

1. No terminal com o ambiente virtual ativado, certifique-se de que está na pasta *src/chat/python.* Instale os pacotes Python necessários utilizando PIP.

    ```bash
    python -m pip install -r requirements.txt
    ```

1. Execute a aplicação de funções.

    ```bash
    func start
    ```

    ![Executar aplicativo de função](media/signalr-quickstart-azure-functions-python/signalr-quickstart-run-application.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, construíste e executaste uma aplicação sem servidor em tempo real no Código VS. Em seguida, saiba mais sobre como implementar as Funções do Azure a partir do VS Code.

> [!div class="nextstepaction"]
> [Implementar as Funções do Azure com o VS Code](/azure/javascript/tutorial-vscode-serverless-node-01)
