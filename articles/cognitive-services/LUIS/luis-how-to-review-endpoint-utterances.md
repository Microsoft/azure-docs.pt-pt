---
title: Examinar usuário declarações-LUIS
titleSuffix: Azure Cognitive Services
description: O aprendizado ativo captura consultas de ponto de extremidade e seleciona o ponto de extremidade do usuário declarações que não tem certeza de. Revise essas declarações para selecionar as entidades de intenção e marca para essas declarações de leitura do mundo. Aceite essas alterações em seu exemplo declarações, em seguida, treine e publique. LUIS, em seguida, identifica declarações com mais precisão.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/25/2019
ms.author: diberry
ms.openlocfilehash: c2f49d4bf573cc2dc2e1a3b8fc13413a738df8ba
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560451"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>Como examinar o ponto de extremidade declarações no LUIS portal for active Learning

O [aprendizado ativo](luis-concept-review-endpoint-utterances.md) captura consultas de ponto de extremidade e seleciona o ponto de extremidade do usuário declarações que não tem certeza de. Revise essas declarações para selecionar as entidades de intenção e marca para essas declarações de leitura do mundo. Aceite essas alterações em seu exemplo declarações, em seguida, treine e publique. LUIS, em seguida, identifica declarações com mais precisão.


## <a name="enable-active-learning"></a>Habilitar o aprendizado ativo

Para habilitar o aprendizado ativo, faça log de consultas de usuário. Isso é feito definindo a [consulta de ponto](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) de extremidade `log=true` com o parâmetro e o valor de QueryString.

## <a name="disable-active-learning"></a>Desabilitar o aprendizado ativo

Para desabilitar o aprendizado ativo, não faça log de consultas de usuário. Isso é feito definindo a [consulta de ponto](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) de extremidade `log=false` com o parâmetro e o valor de QueryString.

## <a name="filter-utterances"></a>Expressões de filtro

1. Abra a sua aplicação (por exemplo, TravelAgent) ao selecionar o respetivo nome na **as minhas aplicações** página, em seguida, selecione **criar** na barra superior.

1. Sob o **melhorar o desempenho da aplicação**, selecione **rever expressões de ponto final**.

1. Na **rever expressões de ponto final** página, selecione no **lista de filtros de intenção ou entidade** caixa de texto. Esta lista pendente inclui todas as intenções sob **INTENÇÕES** e todas as entidades sob **entidades**.

    ![Filtro de expressões](./media/label-suggested-utterances/filter.png)

1. Na lista pendente, selecione uma categoria (intenções ou entidades) e reveja as expressões.

    ![Expressões com intenção](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Entidades de etiqueta
LUIS substitui tokens de entidade (palavras) com os nomes de entidades realçados em azul. Se uma expressão tem sem etiqueta entidades, da etiqueta-los na expressão. 

1. Selecione o word(s) na expressão. 

1. Selecione uma entidade a partir da lista.

    ![Etiqueta de entidade](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Alinhar expressão única

Cada ocorrência de pronunciação tem uma intenção sugerida zobrazené a **alinhadas com a intenção** coluna. 

1. Se concordar com essa sugestão, selecione a marca de verificação.

    ![Manter a intenção alinhada](./media/label-suggested-utterances/align-intent-check.png)

1. Se discordar com a sugestão, selecione a intenção correta da lista pendente intenção alinhada, em seguida, selecione na marca de verificação para a direita da intenção alinhada. 

    ![Alinhar intenção](./media/label-suggested-utterances/align-intent.png)

1. Depois de selecionar a marca de verificação, a expressão é removido da lista. 

## <a name="align-several-utterances"></a>Alinhar expressões com vários

Alinhar expressões com vários, marque a caixa à esquerda das expressões, em seguida, selecione sobre o **adicionar selecionado** botão. 

![Alinhar vários](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Certifique-se a intenção alinhada

Pode verificar a expressão foi alinhado com a intenção correta ao aceder a **intenções** página, selecione o nome de intenção e rever as expressões. A expressão da **rever expressões de ponto final** está na lista.

## <a name="delete-utterance"></a>Eliminar expressão

Cada expressão pode ser eliminado da lista de revisão. Depois de eliminado, não aparecerá na lista novamente. Isso vale mesmo que o usuário insere a expressão mesmo do ponto final. 

Se tiver a certeza se deve excluir a expressão, mova-o para a intenção, nenhuma ou criar uma intenção de novo, como "diversos" e mova a expressão para esse propósito. 

## <a name="delete-several-utterances"></a>Eliminar várias expressões

Para eliminar várias expressões, selecione cada item e selecione do contentor de caixote do lixo à direita dos **adicionar selecionado** botão.

![Eliminar vários](./media/label-suggested-utterances/delete-several.png)


## <a name="next-steps"></a>Passos Seguintes

Para testar como o desempenho aumenta depois coloca expressões sugeridas com uma etiqueta, pode aceder à consola de teste, selecionando **testar** no painel superior. Para obter instruções sobre como testar a sua aplicação utilizando a consola de teste, consulte [treinar e testar a aplicação](luis-interactive-test.md).
