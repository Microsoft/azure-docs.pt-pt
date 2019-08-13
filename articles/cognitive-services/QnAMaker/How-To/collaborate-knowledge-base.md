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
ms.date: 01/14/2019
ms.author: diberry
ms.openlocfilehash: 9c5398ff7cb31698db3d4a798b6a082f9e74b99b
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955140"
---
# <a name="collaborate-on-your-knowledge-base"></a>Colabore em sua base de dados de conhecimento

QnA Maker permite que várias pessoas colaborem em uma base de dados de conhecimento. Esse recurso é fornecido com o [controle de acesso baseado em função](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)do Azure. 

Execute as seguintes etapas para compartilhar seu serviço de QnA Maker com alguém:

1. Entre no portal do Azure e vá para o recurso de QnA Maker.

    ![Lista de recursos do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Vá para a guia **controle de acesso (iam)** .

    ![IAM QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Selecione **Adicionar**.

    ![Adicionar IAM QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Selecione o **proprietário** ou a função **colaborador** . Você não pode conceder acesso somente leitura por meio do controle de acesso baseado em função. A função proprietário e colaborador tem acesso de leitura/gravação direito de QnA Maker serviço.

    ![Adicionar função IAM QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Insira o email com o qual você deseja compartilhar e pressione salvar.

    ![Adicionar email QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Agora, quando a pessoa com a qual você compartilhou seu serviço de QnA Maker, faz logon no [portal de QnA Maker](https://qnamaker.ai) eles podem ver todas as bases de dados de conhecimento nesse serviço.

Lembre-se de que você não pode compartilhar uma base de dados de conhecimento específica em um serviço QnA Maker. Se você quiser um controle de acesso mais granular, considere distribuir suas bases de dados de conhecimento em diferentes serviços de QnA Maker.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Testar uma base de dados de conhecimento](./test-knowledge-base.md)
