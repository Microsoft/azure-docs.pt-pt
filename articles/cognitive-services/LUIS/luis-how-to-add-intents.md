---
title: Adicionar intenções - LUIS
titleSuffix: Azure Cognitive Services
description: Adicione intenções à sua app LUIS para identificar grupos de perguntas ou comandos que tenham as mesmas intenções.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.service: cognitive-services
ms.openlocfilehash: f2401c032f0fc90024e0049fad5f696b8a184544
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018944"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Adicione intenções para determinar a intenção do utilizador de declarações

Adicione [intenções](luis-concept-intent.md) à sua app LUIS para identificar grupos de perguntas ou comandos que tenham a mesma intenção.

No portal LUIS, as intenções são geridas a partir da **secção** build da barra de navegação superior, e depois a partir das **Intenções** do painel esquerdo .

## <a name="add-an-intent-to-your-app"></a>Adicione uma intenção à sua app

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. Na página **Intenções,** selecione **+ Criar**.
1. Na nova caixa de diálogo **de intenção,** insira o nome de intenção, por `ModifyOrder` exemplo, e selecione **'Fazer**' ''

    > [!div class="mx-imgBorder"]
    > ![Adicionar Intenção](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    A intenção precisa de [declarações de exemplo](luis-concept-utterance.md) para prever as declarações no ponto final da previsão publicada.

## <a name="add-an-example-utterance"></a>Adicione um exemplo de expressão

Palavras de exemplo são exemplos de texto de perguntas ou comandos de utilizadores. Para ensinar a Compreensão da Linguagem (LUIS) quando prever esta intenção, é necessário adicionar palavras de exemplo a uma intenção. LUIS precisa no intervalo de 15 a 30 palavras de exemplo para começar a entender a intenção. Não adicione palavras de exemplo a granel. Cada expressão deve ser cuidadosamente escolhida para saber como é diferente dos exemplos já na intenção.

1. Na página de detalhes de intenção, insira uma expressão relevante que espera dos seus utilizadores, como na caixa de `Deliver a large cheese pizza` texto abaixo do nome de intenção, e depois prima Enter.

    > [!div class="mx-imgBorder"]
    > ![Screenshot da página de detalhes do Intenção, com a expressão em destaque](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png)

    LUIS converte todas as expressões em minúsculas e adiciona espaços em torno [de fichas](luis-language-support.md#tokenization) como hífenes.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Erros de previsão de intenção

Um erro de previsão de intenção é determinado quando a expressão não é prevista com a app treinada para a intenção.

1. Para encontrar erros de previsão de expressão e corrigi-los, utilize as opções **de Filtro** de Incorreto e Pouco Claro.

    > [!div class="mx-imgBorder"]
    > ![Para encontrar erros de previsão de expressão e corrigi-los, utilize a opção Filtro.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

1. Para mostrar o valor da pontuação na página De detalhes de Intenção, selecione **Mostrar as pontuações de intenção** do menu de opções **Ver.**

    Quando os filtros e a vista são aplicados, e há palavras de exemplo com erros, a lista de exemplos mostra as expressões e as questões.

Cada linha mostra a previsão do treino atual para o exemplo da expressão, a pontuação do rival mais próximo, que é a diferença nestas duas pontuações.

### <a name="fixing-intents"></a>Fixação de intenções

Para aprender a corrigir erros de previsão de intenção, utilize o [Painel De Resumo](luis-how-to-use-dashboard.md). O painel de resumo fornece análise para o último treino da versão ativa e oferece as principais sugestões para corrigir o seu modelo.

## <a name="using-the-contextual-toolbar"></a>Usando a barra de ferramentas contextual

A barra de ferramentas de contexto fornece outras ações:

* Editar ou apagar a expressão de exemplo
* Reatribuir exemplo de expressão a uma intenção diferente
* Filtros e vistas: apenas mostrar expressões que contenham entidades filtradas ou ver detalhes opcionais
* Pesse através de palavras de exemplo

## <a name="train-your-app-after-changing-model-with-intents"></a>Treine a sua app depois de mudar de modelo com intenções

Depois de adicionar, editar ou remover intenções, [treine](luis-how-to-train.md) e [publique](luis-how-to-publish-app.md) a sua aplicação para que as suas alterações sejam aplicadas em consultas de ponto final. Não treine depois de cada mudança. Treinar depois de um grupo de mudanças.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a adição [de palavras de exemplo](./luis-how-to-add-entities.md) com entidades.