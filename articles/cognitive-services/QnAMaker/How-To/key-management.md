---
title: Recursos e gestão de chaves - QnA Maker
titleSuffix: Azure Cognitive Services
description: O serviço QnA Maker lida com dois tipos de chaves, chaves de subscrição e chaves de ponto final.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b0b82f37b76a6bca6d84a05bc48b7ae9986fb76b
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967688"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>Como gerir as chaves em QnA Maker

O serviço QnA Maker lida com dois tipos de chaves, **chaves de subscrição** e **chaves de ponto final**.

![Gestão de chaves](../media/qnamaker-how-to-key-management/key-management.png)

1. **Chaves de assinatura**: Essas chaves são usadas para acessar as [APIs do serviço de gerenciamento de QnA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Essas APIs permitem que você execute editar sua base de dados de conhecimento.  

2. **Chaves do ponto de extremidade**: Essas chaves são usadas para acessar o ponto de extremidade da base de dados de conhecimento para obter uma resposta para uma pergunta de usuário. Normalmente, você usaria esse ponto de extremidade em seu bot de chat ou no código do aplicativo cliente que consome o serviço de QnA Maker.
 
## <a name="subscription-keys"></a>Chaves de subscrição
Pode ver e repor as chaves de subscrição do portal do Azure onde criou o recurso do QnA Maker. 
1. Vá para o recurso do QnA Maker no portal do Azure.

    ![Lista de recursos do QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Aceda a **chaves**.

    ![Chave de subscrição](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Chaves de ponto final

Chaves de ponto de extremidade podem ser geridas a partir da [portal do QnA Maker](https://qnamaker.ai).

1. Faça logon no [portal de QnA Maker](https://qnamaker.ai), acesse seu perfil e clique em **configurações de serviço**.

    ![Chave de ponto final](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Ver ou repor as suas chaves.

    ![Gestor de chave de ponto final](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Atualize as suas chaves se achar que as mesmas foram comprometidas. Isso pode exigir alterações correspondentes no seu aplicativo cliente ou código de bot.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma base de dados de conhecimento num idioma diferente](./language-knowledge-base.md)
