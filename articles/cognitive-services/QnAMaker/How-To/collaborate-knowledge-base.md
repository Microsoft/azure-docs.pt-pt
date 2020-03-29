---
title: Colaborando na base de conhecimento - QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker permite que várias pessoas colaborem numa base de conhecimento. Esta funcionalidade é fornecida com o Controlo de Acesso baseado em Funções Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: diberry
ms.openlocfilehash: b5adc7ebacde056a141ca3b361b9eb2ea7900a39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75660754"
---
# <a name="collaborate-on-your-knowledge-base"></a>Colabore na sua base de conhecimento

O QnA Maker permite que várias pessoas colaborem em todas as bases de conhecimento no mesmo recurso QnA Maker. Esta funcionalidade é fornecida com o Controlo de [Acesso baseado em funções](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)Azure.

Execute os seguintes passos para partilhar o seu serviço QnA Maker com alguém:

1. Inscreva-se no portal Azure e vá ao seu recurso QnA Maker.

    ![Lista de recursos do Fabricante qna](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Aceda ao separador Controlo de **Acesso (IAM).**

    ![Fabricante de QnA IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Selecione **Adicionar**.

    ![QnA Maker IAM adicionar](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Selecione a função **Proprietário** ou **Colaborador.** Não é possível conceder acesso apenas de leitura através do Controlo de Acesso baseado em funções. As funções de proprietário e colaborador têm permissões de acesso de leitura para o serviço QnA Maker.

    ![QnA Maker IAM adicionar papel](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Introduza o endereço de e-mail do utilizador e prima **Guardar**.

    ![QnA Maker IAM adicionar e-mail](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Quando a pessoa partilhou o seu serviço QnA Maker com, entra no [portal QnA Maker](https://qnamaker.ai) eles podem ver todas as bases de conhecimento desse serviço.

Lembre-se, não pode partilhar uma base de conhecimento particular num serviço QnA Maker. Se quiser um controlo de acesso mais granular, considere distribuir as suas bases de conhecimento por diferentes serviços da QnA Maker.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Testar uma base de dados de conhecimento](./test-knowledge-base.md)
