---
title: Azure SignalR Service sem início de serviço - Python
description: Um quickstart para usar o Serviço Azure SignalR e funções Azure para criar uma sala de chat usando Python.
author: anthonychu
ms.service: signalr
ms.devlang: python
ms.topic: quickstart
ms.date: 12/14/2019
ms.author: antchu
ms.custom: devx-track-python
ms.openlocfilehash: 82a2e64d27fcbbb3801e6dab347343e086a0b70f
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048226"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-python"></a>Quickstart: Criar uma sala de chat com funções Azure e serviço SignalR usando Python

O Serviço Azure SignalR permite-lhe adicionar facilmente funcionalidades em tempo real à sua aplicação. As Funções do Azure são uma plataforma sem servidor que lhe permite executar código sem ter de gerir qualquer infraestrutura. Neste início rápido, saiba como utilizar o Serviço SignalR e as Funções para criar uma aplicação de chat em tempo real sem servidor.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido pode ser executado no macOS, Windows ou Linux.

Certifique-se de que tem um editor de código como o [Visual Studio Code](https://code.visualstudio.com/) instalado.

Instale as [Ferramentas Principais de Funções Azure](https://github.com/Azure/azure-functions-core-tools#installing) (versão 2.7.1505 ou superior) para executar as aplicações Python Azure Function localmente.

As funções Azure requerem [Python 3.6+](https://www.python.org/downloads/). (Ver [versões Python suportadas)](/azure/azure-functions/functions-reference-python#python-version)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qspython)

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em <https://portal.azure.com/> com a sua conta do Azure.

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qspython)

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qspython)

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qspython)

## <a name="configure-and-run-the-azure-function-app"></a>Configurar e executar a aplicação Funções do Azure

1. No browser em que abriu o portal do Azure, certifique-se de que a instância Serviço SignalR implementada anteriormente foi totalmente criada com êxito ao pesquisar o nome na caixa de pesquisa na parte superior do portal. Selecione a instância para abri-la.

    ![Pesquisar a instância do Serviço SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Selecione **Chaves** para ver as cadeias de ligação para a instância do Serviço SignalR.

1. Selecione e copie a cadeia de ligação principal.

    ![Selecione e copie a cadeia de ligação principal.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. No seu editor de código, abra a pasta *src/chat/python* no repositório clonado.

1. Para desenvolver e testar localmente as funções python, você deve trabalhar num ambiente Python 3.6 ou 3.7. Executar os seguintes comandos para criar e ativar um ambiente virtual chamado `.venv` .

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

1. As funções python são organizadas em pastas. Em cada pasta *encontram-se* dois ficheiros:function.jsdefine as encadernações que são utilizadas na função, e *\_ \_ .py \_ \_* é o corpo da função. Existem duas funções acionadas por HTTP nesta aplicação de funções:

    - **negociar** - Utiliza o enlace de entrada *SignalRConnectionInfo* para gerar e devolver informações de ligação válidas.
    - **mensagens** - Recebe uma mensagem de chat no corpo do pedido e utiliza o enlace de saída *SignalR* para difundir a mensagem a todas as aplicações cliente ligadas.

1. No terminal com o ambiente virtual ativado, certifique-se de que está na pasta *src/chat/python.* Instale as embalagens Python necessárias utilizando o PIP.

    ```bash
    python -m pip install -r requirements.txt
    ```

1. Execute a aplicação de funções.

    ```bash
    func start
    ```

    ![Executar app de função](media/signalr-quickstart-azure-functions-python/signalr-quickstart-run-application.png)
    
Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qspython)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qspython)

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Tendo problemas? Experimente o [guia de resolução de problemas](signalr-howto-troubleshoot-guide.md) ou [avise-nos.](https://aka.ms/asrs/qspython)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, você construiu e executou uma aplicação sem servidor em tempo real no Código VS. Em seguida, saiba mais sobre como implementar as Funções do Azure a partir do VS Code.

> [!div class="nextstepaction"]
> [Implementar as Funções do Azure com o VS Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01)

