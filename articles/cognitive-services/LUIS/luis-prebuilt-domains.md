---
title: Domínios pré-concebidos para Understa de idioma
titleSuffix: Azure Cognitive Services
description: LUIS inclui um conjunto de domínios pré-concebidos para adicionar rapidamente os cenários de usuário comum e conversacionais.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: e1b99396c4739dc6f1b7a4da0164553d4c25ef3c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60198944"
---
# <a name="add-prebuilt-domains-for-common-usage-scenarios"></a>Adicionar domínios pré-concebidos para cenários de utilização comuns 

LUIS inclui um conjunto de objetivos pré-criados dos domínios criados previamente para adicionar rapidamente intenções e expressões comuns. Esta é uma forma rápida e fácil adicionar capacidades para a sua aplicação de cliente conversacionais sem ter de modelos para essas habilidades de design. 

## <a name="add-a-prebuilt-domain"></a>Adicionar um domínio pré-concebido

1. Sobre o **as minhas aplicações** página, selecione a sua aplicação. Esta ação abre a aplicação para o **criar** secção da aplicação. 

1. Sobre o **intenções** página, selecione **adicionar domínios pré-concebidos** na parte inferior, esquerda barra de ferramentas. 

1. Selecione o **calendário** intenção, em seguida, selecione **Adicionar domínio** botão.

    ![Adicionar domínio pré-criado do calendário](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. Selecione **intenções** na navegação da esquerda para ver os objetivos de calendário. Cada intenção deste domínio tem o prefixo `Calendar.`. Juntamente com expressões com, as duas entidades para este domínio são adicionadas à aplicação: `Calendar.Location` e `Calendar.Subject`. 

## <a name="train-and-publish"></a>Preparar e publicar

1. Depois do domínio é adicionado, preparar a aplicação, selecionando **treinar** na parte superior, com o botão direito barra de ferramentas. 

1. Na barra de ferramentas superior, selecione **publicar**. Publicar no **produção**. 

1. Quando a notificação de êxito verde é apresentada, selecione o **consulte a lista de pontos de extremidade** ligação para ver os pontos de extremidade.

1. Selecione um ponto de extremidade. É aberto um novo separador do browser para esse ponto final. Mantenha o separador do browser aberta e avance para o **teste** secção.

## <a name="test"></a>Teste

Teste a intenção de novo no ponto final adicionado por um valor para o **p** parâmetro: `Schedule a meeting with John Smith in Seattle next week`.

LUIS retorna a intenção correta e o assunto da reunião:

```json
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Referência de domínio pré-criado](./luis-reference-prebuilt-domains.md)
