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
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: d5652857f2f35e392d3f512001044fd06bc0a0c9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499077"
---
# <a name="how-to-review-endpoint-utterances-in-luis-portal-for-active-learning"></a>Como examinar o ponto de extremidade declarações no LUIS portal for active Learning

O [aprendizado ativo](luis-concept-review-endpoint-utterances.md) captura consultas de ponto de extremidade e seleciona o ponto de extremidade do usuário declarações que não tem certeza de. Revise essas declarações para selecionar as entidades de intenção e marca para essas declarações de leitura do mundo. Aceite essas alterações em seu exemplo declarações, em seguida, treine e publique. LUIS, em seguida, identifica declarações com mais precisão.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="enable-active-learning"></a>Habilitar o aprendizado ativo

Para habilitar o aprendizado ativo, faça log de consultas de usuário. Isso é feito definindo a [consulta de ponto de extremidade](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) com o `log=true` parâmetro e valor de QueryString.

## <a name="disable-active-learning"></a>Desabilitar o aprendizado ativo

Para desabilitar o aprendizado ativo, não faça log de consultas de usuário. Isso é feito definindo a [consulta de ponto de extremidade](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) com o `log=false` parâmetro e valor de QueryString.

## <a name="filter-utterances"></a>Filtrar declarações

1. Abra seu aplicativo (por exemplo, TravelAgent) selecionando seu nome na página **meus aplicativos** e, em seguida, selecione **criar** na barra superior.

1. Em **melhorar o desempenho do aplicativo**, selecione **examinar ponto de extremidade declarações**.

1. Na página **revisar declarações do ponto de extremidade** , selecione na caixa de texto **lista de filtros por intenção ou entidade** . Essa lista suspensa inclui todas as intenções em **tentativas** e todas as entidades em **entidades**.

    ![Filtro de declarações](./media/label-suggested-utterances/filter.png)

1. Selecione uma categoria (intenções ou entidades) na lista suspensa e examine o declarações.

    ![Declarações intenção](./media/label-suggested-utterances/intent-utterances.png)

## <a name="label-entities"></a>Rotular entidades
LUIS substitui os tokens de entidade (palavras) por nomes de entidade realçados em azul. Se um expressão tiver entidades sem rótulo, etiquete-as no expressão. 

1. Selecione uma das palavras no expressão. 

1. Selecione uma entidade na lista.

    ![Entidade de rótulo](./media/label-suggested-utterances/label-entity.png)

## <a name="align-single-utterance"></a>Alinhar expressão único

Cada expressão tem uma intenção sugerida exibida na coluna de **intenção alinhada** . 

1. Se você concordar com essa sugestão, selecione na marca de seleção.

    ![Manter a intenção alinhada](./media/label-suggested-utterances/align-intent-check.png)

1. Se você discordar da sugestão, selecione a intenção correta na lista suspensa intenção alinhada e selecione na marca de seleção à direita da intenção alinhada. 

    ![Intenção de alinhamento](./media/label-suggested-utterances/align-intent.png)

1. Depois de selecionar na marca de seleção, o expressão é removido da lista. 

## <a name="align-several-utterances"></a>Alinhar várias declarações

Para alinhar vários declarações, marque a caixa à esquerda de declarações e, em seguida, selecione no botão **Adicionar selecionado** . 

![Alinhar vários](./media/label-suggested-utterances/add-selected.png)

## <a name="verify-aligned-intent"></a>Verificar intenção alinhada

Você pode verificar se o expressão foi alinhado com a intenção correta acessando a página de **tentativas** , selecionando o nome da intenção e revisando o declarações. O expressão do **ponto de extremidade de revisão declarações** está na lista.

## <a name="delete-utterance"></a>Excluir expressão

Cada expressão pode ser excluído da lista de revisão. Depois de excluído, ele não será exibido na lista novamente. Isso é verdadeiro mesmo que o usuário insira o mesmo expressão do ponto de extremidade. 

Se você não tiver certeza se deve excluir o expressão, mova-o para a intenção nenhum ou crie uma nova intenção, como "Miscelânea", e mova o expressão para essa intenção. 

## <a name="delete-several-utterances"></a>Excluir várias declarações

Para excluir vários declarações, selecione cada item e selecione na lixeira à direita do botão **Adicionar selecionado** .

![Excluir vários](./media/label-suggested-utterances/delete-several.png)


## <a name="next-steps"></a>Passos seguintes

Para testar como o desempenho melhora depois de você rotular declarações sugeridos, você pode acessar o console de teste selecionando **testar** no painel superior. Para obter instruções sobre como testar seu aplicativo usando o console de teste, consulte [treinar e testar seu aplicativo](luis-interactive-test.md).
