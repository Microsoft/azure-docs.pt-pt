---
title: Recursos e gestão de chaves - QnA Maker
titleSuffix: Azure Cognitive Services
description: O serviço QnA Maker lida com dois tipos de chaves, chaves de subscrição e chaves de ponto final.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b9be1db9be1d4dd57994e101c07ed430425a5912
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447430"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>Como gerir as chaves em QnA Maker

O serviço QnA Maker lida com dois tipos de chaves, **chaves de subscrição** e **chaves de ponto final**.

![Gestão de chaves](../media/qnamaker-how-to-key-management/key-management.png)

1. **Chaves de subscrição**: Estas chaves são utilizadas para aceder a [APIs de serviço de gestão do QnA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Essas APIs permitem que faça editar a sua base de dados de conhecimento.  

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
