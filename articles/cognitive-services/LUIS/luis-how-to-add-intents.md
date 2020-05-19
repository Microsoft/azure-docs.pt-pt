---
title: Adicionar intenções - LUIS
titleSuffix: Azure Cognitive Services
description: Adicione intenções à sua app LUIS para identificar grupos de perguntas ou comandos que tenham as mesmas intenções.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: b2cb7494ae3d26fa14bef906b8f5222b9dbc70e1
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584980"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Adicione intenção sintetmente para determinar a intenção do utilizador de declarações

Adicione [intenção](luis-concept-intent.md) à sua app LUIS para identificar grupos de perguntas ou comandos que tenham a mesma intenção.

As intenções são geridas a partir da secção de **construção** da barra de navegação superior, em seguida, a partir das **Intenções**do painel esquerdo .

## <a name="add-intent"></a>Adicionar intenções

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **De Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. Na página **Intenções,** selecione **+ Criar**.
1. Na nova caixa de diálogo de **intenção Criar,** introduza o nome da intenção, por `ModifyOrder` exemplo, e selecione **Done**.

    > [!div class="mx-imgBorder"]
    > ![Adicionar Intenção](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    A intenção precisa de declarações exemplo.

## <a name="add-an-example-utterance"></a>Adicione um exemplo de expressão

As expressões exemplo são exemplos de texto de perguntas ou comandos do utilizador. Para ensinar a Compreensão da Linguagem (LUIS) quando prever esta intenção, é preciso adicionar frases de exemplo a uma intenção. Luis precisa de 15 a 30 exemplos para começar a entender a intenção. Não adicione pronunciações a granel. Cada expressão deve ser cuidadosamente escolhida para a forma como é diferente dos exemplos já na intenção.

1. Na página de detalhes de intenção, introduza uma expressão relevante que espera dos seus utilizadores, como `Deliver a large cheese pizza` na caixa de texto abaixo do nome de intenção e, em seguida, prima Enter.

    > [!div class="mx-imgBorder"]
    > ![Screenshot da página de detalhes de Intenções, com expressão em destaque](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png)

    Luis converte todas as expressões em minúsculas e adiciona espaços em torno de [tokens](luis-language-support.md#tokenization) como hífenes.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Erros de previsão de intenção

Um exemplo de expressão numa intenção pode ter um erro de previsão de intenção entre a intenção em que a expressão do exemplo está atualmente e a intenção determinada durante o treino.

Para encontrar erros de previsão de expressão e corrigi-los, utilize as opções de **filtro** de 'Incorreto e Unclear' combinadas com a opção **Ver** de **vista detalhada**.

![Para encontrar erros de previsão de expressão e corrigi-los, utilize a opção Filter.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Quando os filtros e a vista são aplicados, e há declarações exemplocom erros, a lista de pronunciamentos exemplo mostra as expressões e as questões.

> [!div class="mx-imgBorder"]
> ![! [Quando os filtros e a vista são aplicados, e há declarações exemplo com erros, a lista de pronunciamentos exemplo mostra as expressões e as questões.] (./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Cada linha mostra a pontuação de previsão do treino atual para a expressão do exemplo, a pontuação do rival mais próximo, que é a diferença nestas duas pontuações.

### <a name="fixing-intents"></a>Fixação de intenções

Para aprender a corrigir erros de previsão de intenções, utilize o [Dashboard Resumo](luis-how-to-use-dashboard.md). O painel de instrumentos de resumo fornece análise para o último treino da versão ativa e oferece as principais sugestões para corrigir o seu modelo.

## <a name="using-the-contextual-toolbar"></a>Utilização da barra de ferramentas contextual

A barra de ferramentas de contexto fornece outras ações:

* Editar ou excluir a expressão do exemplo
* Reatribuir a expressão do exemplo a uma intenção diferente
* Filtros e vistas: apenas mostrar expressões que contenham entidades filtradas ou ver detalhes opcionais
* Pesquisar através de declarações de exemplo

## <a name="train-your-app-after-changing-model-with-intents"></a>Treine a sua app depois de mudar de modelo com intenções

Depois de adicionar, editar ou remover intenções, [treinar](luis-how-to-train.md) e [publicar](luis-how-to-publish-app.md) a sua aplicação para que as suas alterações sejam aplicadas a consultas de ponto final. Não treine depois de cada mudança. Treinar depois de um grupo de mudanças.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre adicionar [declarações](luis-how-to-add-example-utterances.md) de exemplo com entidades.
