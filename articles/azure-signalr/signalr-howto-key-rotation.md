---
title: Como rodar a chave de acesso para o serviço Azure SignalR
description: Uma visão geral sobre o motivo pelo qual o cliente tem rotineiramente rodar as chaves de acesso e de como fazê-lo com o portal do Azure GUI e a CLI do Azure.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 15b0ff0bbb96e5fa96d81cfa265e83abf749cf85
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555273"
---
# <a name="how-to-rotate-access-key-for-azure-signalr-service"></a>Como rodar a chave de acesso para o serviço Azure SignalR

Cada instância do serviço Azure SignalR tem um par de chaves de acesso denominado chaves primária e secundária. Eles são usados para autenticar clientes de SignalR quando os pedidos são efetuados para o serviço. As chaves estão associadas com o url de ponto final de instância. Mantenha as suas chaves seguras e girá-las regularmente. É fornecido com duas chaves de acesso, por isso, possa manter as ligações ao utilizar uma chave enquanto Regenera a outra.

## <a name="why-rotate-access-keys"></a>Por que motivo Rodar chaves de acesso?

Para questões de segurança e os requisitos de conformidade, rotineiramente roda as chaves de acesso.

## <a name="regenerate-access-keys"></a>Voltar a gerar chaves de acesso

1. Vá para o [portal do Azure](https://portal.azure.com/)e inicie sessão com as suas credenciais.

1. Encontrar o **chaves** secção na instância do serviço Azure SignalR com as chaves que pretende voltar a gerar.

1. Selecione **chaves** no menu de navegação.

1. Selecione **regenerar a chave primária** ou **voltar a gerar chave secundária**.

   Uma nova chave e a cadeia de ligação correspondente são criados e apresentados.

   ![Regenerar Chaves](media/signalr-howto-key-rotation/regenerate-keys.png)

Também pode voltar a gerar chaves utilizando o [CLI do Azure](/cli/azure/ext/signalr/signalr/key?view=azure-cli-latest#ext-signalr-az-signalr-key-renew).

## <a name="update-configurations-with-new-connection-strings"></a>Configurações de atualização com novas cadeias de ligação

1. Copie a cadeia de ligação gerados recentemente.

1. Atualize todas as configurações para utilizar a nova cadeia de ligação.

1. Reinicie a aplicação conforme necessário.

## <a name="forced-access-key-regeneration"></a>Regeneração da chave de acesso forçado

Serviço Azure SignalR poderá impor uma regeneração da chave de acesso obrigatórios em determinadas situações. O serviço notifica os clientes por e-mail e notificação do portal. Se receber esta comunicação ou se ocorrer falha de serviço devido a uma chave de acesso, roda as chaves ao seguir as instruções neste guia.

## <a name="next-steps"></a>Passos Seguintes

Roda as chaves de acesso regularmente como uma boa prática de segurança.

Neste guia, aprendeu a voltar a gerar chaves de acesso. Avance para os tutoriais seguintes sobre a autenticação OAuth ou com as funções do Azure.

> [!div class="nextstepaction"]
> [Integrar com a identidade do ASP.NET core](./signalr-concept-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Criar uma aplicação sem servidor em tempo real com a autenticação](./signalr-tutorial-authenticate-azure-functions.md)