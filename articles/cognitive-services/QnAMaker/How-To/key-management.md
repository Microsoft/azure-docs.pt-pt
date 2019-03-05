---
title: Recursos e gestão de chaves - QnA Maker
titleSuffix: Azure Cognitive Services
description: O serviço QnA Maker lida com dois tipos de chaves, chaves de subscrição e chaves de ponto final.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: c1d8104f94d3f215108000b904848f3b968492ee
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341919"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>Como gerir as chaves em QnA Maker

O serviço QnA Maker lida com dois tipos de chaves, **chaves de subscrição** e **chaves de ponto final**.

![Gestão de chaves](../media/qnamaker-how-to-key-management/key-management.png)

1. **Chaves de subscrição**: Estas chaves são utilizadas para aceder a [APIs de serviço de gestão do QnA Maker](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff). Essas APIs permitem que faça editar a sua base de dados de conhecimento.  

2. **Chaves de ponto final**: Estas chaves são utilizadas para aceder ao ponto final de base de dados de conhecimento para obter uma resposta para uma pergunta do utilizador. Normalmente usaria este ponto final no seu bot de bate-papo, ou o código de aplicação de cliente que consome o serviço QnA Maker.
 
## <a name="subscription-keys"></a>Chaves de subscrição
Pode ver e repor as chaves de subscrição do portal do Azure onde criou o recurso do QnA Maker. 
1. Vá para o recurso do QnA Maker no portal do Azure.

    ![Lista de recursos do QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Aceda a **chaves**.

    ![Chave de subscrição](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Chaves de ponto final

Chaves de ponto de extremidade podem ser geridas a partir da [portal do QnA Maker](https://qnamaker.ai).

1. Inicie sessão para o [portal do QnA Maker](https://qnamaker.ai), aceda ao seu perfil e, em seguida, clique em **definições do serviço**.

    ![Chave de ponto final](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Ver ou repor as suas chaves.

    ![Gestor de chave de ponto final](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Atualize as suas chaves se achar que as mesmas foram comprometidas. Isso pode exigir alterações correspondentes para o código de bot ou aplicação cliente.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma base de dados de conhecimento num idioma diferente](./language-knowledge-base.md)
