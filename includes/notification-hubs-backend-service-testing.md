---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 5e75c5d5510f596eb7911cae0310e60b6bef67bf
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146469"
---
### <a name="send-a-test-notification"></a>Enviar uma notificação de teste

1. Abra uma nova conta no [Carteiro.](https://www.postman.com/downloads/)

1. Desaça o pedido ao **POST,** e insira o seguinte endereço:

    ```xml
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

1. Se optar por completar os [clientes Authenticate utilizando uma secção chave API,](#authenticate-clients-using-an-api-key-optional) certifique-se de configurar os cabeçalhos de pedido para incluir o seu valor **apikey.**

   | Chave                            | Valor                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. Escolha a opção **bruta** para o **Corpo,** em seguida, escolha **JSON** na lista de opções de formato e, em seguida, inclua algum conteúdo de espaço reservado **JSON:**

    ```json
    {
        "text": "Message from Postman!",
        "action": "action_a"
    }
    ```

1. Selecione o botão **Código,** que está sob o botão **Guardar** no canto superior direito da janela. O pedido deve ser semelhante ao exemplo a seguir quando apresentado para **HTML** (dependendo se incluiu um cabeçalho **apikey):**

    ```html
    POST /api/notifications/requests HTTP/1.1
    Host: https://<app_name>.azurewebsites.net
    apikey: <your_api_key>
    Content-Type: application/json

    {
        "text": "Message from backend service",
        "action": "action_a"
    }
    ```

1. Execute a aplicação **PushDemo** em uma ou ambas as plataformas-alvo **(Android** e **iOS).**

    > [!NOTE]
    > Se estiver a testar no **Android** certifique-se de que não está a correr no **Debug**– ou se a aplicação foi implementada executando a aplicação, então force a fechar a app e reinicie a partir do lançador.

1. Na aplicação **PushDemo,** toque no botão **Registar.**

1. De volta ao **[Carteiro,](https://www.postman.com/downloads)** feche a janela **'Gerar Código Snippets'** (se ainda não o fez) e clique no botão **Enviar.**

1. Valide que obtém uma resposta **de 200 OK** no **[Carteiro](https://www.postman.com/downloads)** e o alerta aparece na aplicação que mostra a ação da **ActionA recebida.**  

1. Feche a aplicação **PushDemo** e, em seguida, clique no botão **Enviar** novamente no **[Carteiro](https://www.postman.com/downloads)**.

1. Valide que recebe uma resposta **de 200 OK** no **[Carteiro](https://www.postman.com/downloads)** novamente. Validar que uma notificação aparece na área de notificação da aplicação **PushDemo** com a mensagem correta.

1. Toque na notificação para confirmar que abre a app e exibiu a **ação actionA recebida** alerta.

1. De volta ao **[Carteiro,](https://www.postman.com/downloads)** modifique o órgão de pedido anterior para enviar uma notificação silenciosa especificando *action_b* em vez de *action_a* pelo valor de **ação.**

    ```json
    {
        "action": "action_b",
        "silent": true
    }
    ```

1. Com a aplicação ainda aberta, clique no botão **Enviar** no **[Carteiro](https://www.postman.com/downloads)**.

1. Valide que obtém uma resposta **de 200 OK** no **[Carteiro](https://www.postman.com/downloads)** e que o alerta aparece na aplicação que mostra a **ação actionB recebida** em vez da ação da **ActionA recebida.**

1. Feche a aplicação **PushDemo** e, em seguida, clique no botão **Enviar** novamente no **[Carteiro](https://www.postman.com/downloads)**.

1. Valide que obtém uma resposta **de 200 OK** no **[Carteiro](https://www.postman.com/downloads)** e que a notificação silenciosa não aparece na área de notificação.
