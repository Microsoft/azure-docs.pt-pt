---
title: Colaborar na base de dados de conhecimento - Qna Maker
titleSuffix: Azure Cognitive Services
description: A ferramenta QnA Maker permite que várias pessoas colaborar numa base de dados de conhecimento. Esta funcionalidade é fornecida com o controlo de acesso.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.openlocfilehash: ca754f197a46fc41b6f1b432611a2177ec0afafa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61374862"
---
# <a name="collaborate-on-your-knowledge-base"></a>Colaborar na sua base de dados de conhecimento

A ferramenta QnA Maker permite que várias pessoas colaborar numa base de dados de conhecimento. Esta funcionalidade é fornecida com o Azure [controlo de acesso baseado em funções](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure). 

Execute os seguintes passos para partilhar o seu serviço QnA Maker com alguém:

1. Inicie sessão no portal do Azure e aceda ao seu recurso do QnA Maker.

    ![Lista de recursos do QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Vá para o **controlo de acesso (IAM)** separador.

    ![A ferramenta QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Selecione **Adicionar**.

    ![Adicionar o QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Selecione o **proprietário** ou o **contribuinte** função. Não é possível conceder acesso só de leitura por meio do controle de acesso baseado em funções. Função de proprietário e contribuinte tem acesso de leitura / escrita diretamente para o serviço QnA Maker.

    ![QnA Maker IAM Adicionar função](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Introduza o e-mail que pretende partilhar com e pressiono salvar.

    ![QnA Maker IAM adicionar e-mail](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Agora quando a pessoa partilhou o serviço QnA Maker com, inicia sessão na [portal do QnA Maker](https://qnamaker.ai) podem ver todas as bases de dados de conhecimento desse serviço.

Lembre-se de que não é possível partilhar uma base de dados de conhecimento específica num serviço QnA Maker. Se pretender que o controlo de acesso mais granular, considere distribuir as suas bases de dados de conhecimento em todos os diferentes serviços do QnA Maker.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Testar uma base de dados de conhecimento](./test-knowledge-base.md)
