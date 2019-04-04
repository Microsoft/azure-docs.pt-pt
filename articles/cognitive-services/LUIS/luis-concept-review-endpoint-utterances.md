---
title: Expressão de utilizador de revisão
titleSuffix: Language Understanding - Azure Cognitive Services
description: Com a aprendizagem ativa, seus discursos de ponto final de revisão para intenção correta e de entidade. LUIS escolhe expressões de ponto final é verificá-lo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: 2af11d7776a29288801e5db049262481ae27c102
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893695"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Conceitos para ativar a aprendizagem ativa, revendo os discursos de ponto final
Aprendizagem ativa é uma das três estratégias para melhorar a exatidão da previsão e o mais fácil de implementar. Com a aprendizagem ativa, seus discursos de ponto final de revisão para intenção correta e de entidade. LUIS escolhe expressões de ponto final é verificá-lo.

## <a name="what-is-active-learning"></a>O que é a aprendizagem ativa
Aprendizagem ativa é um processo de dois passos. Em primeiro lugar, LUIS seleciona expressões que recebe no ponto final da aplicação que necessitam de validação. O segundo passo é efetuado pelo proprietário da aplicação ou funcionário para validar as expressões selecionadas para [rever](luis-how-to-review-endpoint-utterances.md), incluindo a intenção correta e todas as entidades na intenção. Depois de rever as expressões, formar e publicar a aplicação novamente. 

## <a name="which-utterances-are-on-the-review-list"></a>As expressões são na lista de revisão
LUIS adiciona expressões com a lista de revisão quando a parte superior disparando intenção tem uma pontuação baixa ou pontuações de principais dois objetivos estão muito próximos. 

## <a name="single-pool-for-utterances-per-app"></a>Conjunto único para expressões com por aplicação
O **rever expressões de ponto final** lista não é alterada com base na versão. Há um único conjunto de expressões para rever, independentemente da versão da expressão que está ativamente a editar ou da versão da aplicação que foi publicada no ponto final. 

## <a name="where-are-the-utterances-from"></a>Onde estão as expressões de
Expressões de ponto de extremidade são obtidas a partir de consultas de utilizador final no ponto final HTTP do aplicativo. Se a aplicação não está publicada ou não recebeu pedidos com êxito na ainda, não tem quaisquer expressões de com para rever. Se não existem resultados de ponto de extremidade são recebidos para um objetivo específico ou uma entidade, não tem expressões para rever o que os contêm. 

## <a name="schedule-review-periodically"></a>Revisão de agenda periodicamente
Rever expressões sugeridas com não precisa ser feito a todos os dias, mas deve fazer parte da manutenção regular de LUIS. 

## <a name="delete-review-items-programmatically"></a>Eliminar itens de revisão através de programação
Utilize o **[eliminar expressões com unlabelled](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** API. Cópia de segurança essas expressões com antes da eliminação por  **[exportar os ficheiros de registo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)**.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [rever](luis-how-to-review-endpoint-utterances.md) expressões de ponto final
