---
title: incluir ficheiro
description: incluir ficheiro
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 03/04/2019
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 73d4d4e1a5f148dce6099b2d747ee1c290bcf7c1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67184630"
---
## <a name="run-the-web-application"></a>Execute a aplicação Web

1. Há uma aplicação Web de página única de exemplo alojada no GitHub para sua conveniência. Abra o [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/)seu navegador para .

    > [!NOTE]
    > A fonte do ficheiro HTML está localizada em [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html).

1. Quando lhe for pedido o URL base da aplicação de funções, introduza `http://localhost:7071`.

1. Introduza um nome de utilizador quando lhe for pedido.

1. A aplicação Web chama a função *GetSignalRInfo* na aplicação de funções para obter as informações de ligação para ligar ao Azure SignalR Service. Quando a ligação estiver concluída, é apresentada a caixa de entrada de mensagens do chat.

1. Escreva uma mensagem e prima enter. A aplicação envia a mensagem para a função *SendMessage* na aplicação de Funções do Azure que, em seguida, utiliza o enlace de saída do SignalR para transmitir a mensagem a todos os clientes ligados. Se tudo estiver a funcionar corretamente, deve aparecer a mensagem na aplicação.

    ![Executar a aplicação](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Abra outra instância da aplicação Web noutra janela do browser. Verá que todas as mensagens enviadas irão aparecer em todas as instâncias da aplicação.

> [!IMPORTANT]
> Uma vez que a página HTML é servida usando HTTPS, mas o tempo de funcionamento das Funções Azure locais está a utilizar http por padrão, o seu navegador (como o Firefox) pode impor uma política de conteúdo misto que bloqueia os pedidos da página web para as suas funções. Para resolver isto, utilize um navegador que não tenha esta restrição ou inicie um servidor HTTP local, como [http-server](https://www.npmjs.com/package/http-server) no diretório */docs/demo/chat-v2.* Certifique-se de que `CORS` a origem é adicionada à definição em *local.settings.json*.