---
title: Como rodar a chave de acesso do Azure SignalR Service
description: Uma visão geral do porquê do cliente precisar de rodar rotineiramente as teclas de acesso e como fazê-lo com o portal Azure GUI e o Azure CLI.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 133edc64ac2f858a397a4a184c24497dae8af333
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67565735"
---
# <a name="how-to-rotate-access-key-for-azure-signalr-service"></a>Como rodar a chave de acesso do Azure SignalR Service

Cada instância de serviço de sinalização Azure tem um par de teclas de acesso chamadas chaves primárias e secundárias. São usados para autenticar clientes SignalR quando são feitos pedidos ao serviço. As teclas estão associadas ao url do ponto final da instância. Mantenha as chaves seguras e rode-as regularmente. Tem duas teclas de acesso, para que possa manter as ligações usando uma tecla enquanto regenera a outra.

## <a name="why-rotate-access-keys"></a>Por que rodar as chaves de acesso?

Por razões de segurança e requisitos de conformidade, rode rotineiramente as suas chaves de acesso.

## <a name="regenerate-access-keys"></a>Chaves de acesso regeneradas

1. Vá ao [portal Azure](https://portal.azure.com/)e inscreva-se com as suas credenciais.

1. Encontre a secção **Keys** na instância de Serviço De Sinalização Azure com as teclas que pretende regenerar.

1. Selecione **Teclas** no menu de navegação.

1. **Selecione Chave Primária Regenerar** ou **Regenerar a Chave Secundária**.

   Uma nova chave e uma corda de ligação correspondente são criadas e exibidas.

   ![Chaves regeneradas](media/signalr-howto-key-rotation/regenerate-keys.png)

Também pode regenerar as teclas utilizando o [Azure CLI](/cli/azure/signalr/key?view=azure-cli-latest#az-signalr-key-renew).

## <a name="update-configurations-with-new-connection-strings"></a>Configurações de atualização com novas cordas de ligação

1. Copie a cadeia de ligação recém-gerada.

1. Atualize todas as configurações para utilizar a nova cadeia de ligação.

1. Reinicie a aplicação conforme necessário.

## <a name="forced-access-key-regeneration"></a>Regeneração da chave de acesso forçado

O Serviço De Sinalização Azure pode impor uma regeneração de chave de acesso obrigatória em determinadas situações. O serviço notifica os clientes através de notificação de e-mail e portal. Se receber esta falha de comunicação ou de encontro devido a uma chave de acesso, rode as teclas seguindo as instruções deste guia.

## <a name="next-steps"></a>Passos seguintes

Rode regularmente as chaves de acesso como uma boa prática de segurança.

Neste guia, aprendeu a regenerar as chaves de acesso. Continue para os próximos tutoriais sobre autenticação com OAuth ou com Funções Azure.

> [!div class="nextstepaction"]
> [Integrar com ASP.NET identidade central](./signalr-concept-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Construa uma aplicação em tempo real sem servidores com autenticação](./signalr-tutorial-authenticate-azure-functions.md)