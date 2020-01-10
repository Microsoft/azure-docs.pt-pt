---
title: Colaborando na base de dados de conhecimento-QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker permite que várias pessoas colaborem em uma base de dados de conhecimento. Esse recurso é fornecido com o controle de acesso baseado em função do Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: diberry
ms.openlocfilehash: b5adc7ebacde056a141ca3b361b9eb2ea7900a39
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660754"
---
# <a name="collaborate-on-your-knowledge-base"></a>Colabore em sua base de dados de conhecimento

QnA Maker permite que várias pessoas colaborem em todas as bases de dados de conhecimento no mesmo recurso de QnA Maker. Esse recurso é fornecido com o [controle de acesso baseado em função](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)do Azure.

Execute as seguintes etapas para compartilhar seu serviço de QnA Maker com alguém:

1. Entre no portal do Azure e vá para o recurso de QnA Maker.

    ![Lista de recursos do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Vá para a guia **controle de acesso (iam)** .

    ![IAM QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Selecione **Adicionar**.

    ![Adicionar IAM QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Selecione o **proprietário** ou a função **colaborador** . Você não pode conceder acesso somente leitura por meio do controle de acesso baseado em função. As funções de proprietário e colaborador têm permissões de acesso de leitura/gravação para o serviço de QnA Maker.

    ![Adicionar função IAM QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Insira o endereço de email do usuário e pressione **salvar**.

    ![Adicionar email QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Quando a pessoa, você compartilhou seu serviço de QnA Maker com, faz logon no [portal de QnA Maker](https://qnamaker.ai) eles podem ver todas as bases de dados de conhecimento nesse serviço.

Lembre-se de que você não pode compartilhar uma base de dados de conhecimento específica em um serviço QnA Maker. Se você quiser um controle de acesso mais granular, considere distribuir suas bases de dados de conhecimento em diferentes serviços de QnA Maker.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Testar uma base de dados de conhecimento](./test-knowledge-base.md)
