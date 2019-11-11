---
title: Adicionar tentativas-LUIS
titleSuffix: Azure Cognitive Services
description: Adicione tentativas ao seu aplicativo LUIS para identificar grupos de perguntas ou comandos que têm as mesmas intenções.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 66a3350dee60772ce706af8995179dcd8c485b64
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904330"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Adicionar tentativas para determinar a intenção do usuário de declarações

Adicione [intenções](luis-concept-intent.md) ao seu aplicativo Luis para identificar grupos de perguntas ou comandos que têm a mesma intenção. 

As intenções são gerenciadas na seção de **Build** da barra de navegação superior e, em seguida, das **tentativas**do painel esquerdo. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-intent"></a>Adicionar intenções

1. No [portal de visualização do Luis](https://preview.luis.ai), selecione **Compilar** para exibir tentativas. 
1. Na página **tentativas** , selecione **+ criar**.
1. Na caixa de diálogo **criar nova tentativa** , insira o nome da intenção, por exemplo `ModifyOrder`, e selecione **concluído**.

    > [!div class="mx-imgBorder"]
    > ![adicionar tentativa](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    A intenção precisa de um exemplo de declarações.

## <a name="add-an-example-utterance"></a>Adicionar um exemplo de expressão

Exemplo declarações são exemplos de texto de perguntas ou comandos do usuário. Para ensinar Reconhecimento vocal (LUIS) quando prever essa intenção, você precisa adicionar o declarações de exemplo a uma intenção. O LUIS precisa do intervalo de 15 a 30 exemplos de declarações para começar a entender a intenção. Não adicione o exemplo declarações em massa. Cada expressão deve ser escolhido cuidadosamente para como ele é diferente dos exemplos já existentes. 

1. Na página detalhes da intenção, insira um expressão relevante que você espera de seus usuários, como `Deliver a large cheese pizza` na caixa de texto abaixo do nome da intenção e pressione Enter.
 
    > [!div class="mx-imgBorder"]
    > ![captura de tela da página de detalhes de tentativas, com expressão realçado](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS converte todas as declarações para minúsculas e adiciona espaços em volta de [tokens](luis-language-support.md#tokenization) como hifens.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Erros de previsão de intenção 

Um exemplo de expressão em uma intenção pode ter um erro de previsão de intenção entre a intenção em que o exemplo expressão está atualmente em e a intenção determinada durante o treinamento. 

Para localizar erros de previsão do expressão e corrigi-los, use as opções de **filtro** de incorretas e não claras combinadas com a opção **Exibir** da **exibição detalhada**. 

![Para localizar erros de previsão de expressão e corrigi-los, use a opção de filtro.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Quando os filtros e a exibição são aplicados e há um exemplo de declarações com erros, a lista de expressão de exemplo mostra o declarações e os problemas.

> [!div class="mx-imgBorder"]
> ![! [Quando os filtros e a exibição são aplicados e há um exemplo de declarações com erros, a lista de expressão de exemplo mostra o declarações e os problemas.] (./Media/Luis-How-to-add-intents/Find-Errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Cada linha mostra a pontuação de previsão do treinamento atual para o exemplo expressão, a Pontuação do rival mais próximo, que é a diferença nessas duas pontuações. 

### <a name="fixing-intents"></a>Corrigindo tentativas

Para saber como corrigir erros de previsão de intenção, use o [painel de resumo](luis-how-to-use-dashboard.md). O painel de resumo fornece análise para o último treinamento da versão ativa e oferece as principais sugestões para corrigir seu modelo.  

## <a name="using-the-contextual-toolbar"></a>Usando a barra de ferramentas contextual

A barra de ferramentas de contexto fornece outras ações:

* Editar ou excluir exemplo expressão
* Reatribuir expressão de exemplo a uma intenção diferente
* Filtros e exibições: mostrar somente declarações que contêm entidades filtradas ou exibir detalhes opcionais
* Pesquisar por meio de exemplo declarações

## <a name="train-your-app-after-changing-model-with-intents"></a>Treinar seu aplicativo depois de alterar o modelo com tentativas

Depois de adicionar, editar ou remover tentativas, [treine](luis-how-to-train.md) e [publique](luis-how-to-publish-app.md) seu aplicativo para que suas alterações sejam aplicadas às consultas de ponto de extremidade. Não treine após cada alteração única. Treine após um grupo de alterações. 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como adicionar [declarações de exemplo](luis-how-to-add-example-utterances.md) com entidades. 
