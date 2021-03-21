---
title: Como rodar a chave de acesso do Azure SignalR Service
description: Uma visão geral sobre o porquê do cliente necessitar de rodar rotineiramente as teclas de acesso e como fazê-lo com o portal Azure GUI e o Azure CLI.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 1f80742d48fc46c8c5e337794100ca5901818c88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184394"
---
# <a name="how-to-rotate-access-key-for-azure-signalr-service"></a>Como rodar a chave de acesso do Azure SignalR Service

Cada instância do Serviço Azure SignalR tem um par de chaves de acesso chamadas teclas primárias e secundárias. São usados para autenticar clientes SignalR quando são feitos pedidos ao serviço. As teclas estão associadas com o url de ponta de exemplo. Mantenha as chaves seguras e rode-as regularmente. Tem duas chaves de acesso, para que possa manter as ligações usando uma chave enquanto regenera a outra.

## <a name="why-rotate-access-keys"></a>Por que rodar as chaves de acesso?

Por razões de segurança e requisitos de conformidade, rode rotineiramente as suas teclas de acesso.

## <a name="regenerate-access-keys"></a>Regenerar chaves de acesso

1. Vá ao [portal Azure](https://portal.azure.com/)e assine com as suas credenciais.

1. Encontre a secção **Chaves** na placa do Serviço Azure SignalR com as teclas que pretende regenerar.

1. Selecione **Teclas** no menu de navegação.

1. Selecione **Regenerar a chave primária** ou **regenerar a chave secundária**.

   Uma nova chave e uma cadeia de ligação correspondente são criadas e exibidas.

   ![Chaves Regeneração](media/signalr-howto-key-rotation/regenerate-keys.png)

Também pode regenerar chaves utilizando o [Azure CLI](/cli/azure/signalr/key#az-signalr-key-renew).

## <a name="update-configurations-with-new-connection-strings"></a>Atualizar configurações com novas cadeias de ligação

1. Copie a cadeia de ligação recém-gerada.

1. Atualize todas as configurações para utilizar a nova cadeia de ligação.

1. Reinicie a aplicação conforme necessário.

## <a name="forced-access-key-regeneration"></a>Regeneração de chaves de acesso forçado

O Serviço Azure SignalR pode impor uma regeneração obrigatória da chave de acesso em determinadas situações. O serviço notifica os clientes através de e-mail e notificação do portal. Se receber esta comunicação ou encontrar uma falha de serviço devido a uma chave de acesso, rode as teclas seguindo as instruções deste guia.

## <a name="next-steps"></a>Passos seguintes

Rode regularmente as suas teclas de acesso como uma boa prática de segurança.

Neste guia, aprendeu a regenerar as chaves de acesso. Continue para os seguintes tutoriais sobre autenticação com OAuth ou com Funções Azure.

> [!div class="nextstepaction"]
> [Integre-se com ASP.NET identidade central](./signalr-concept-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Criar uma aplicação sem servidor em tempo real com a autenticação](./signalr-tutorial-authenticate-azure-functions.md)