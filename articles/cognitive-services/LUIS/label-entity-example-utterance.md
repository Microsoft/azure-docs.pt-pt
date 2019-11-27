---
title: Exemplo de entidade de rótulo expressão
titleSuffix: Azure Cognitive Services
description: Saiba como rotular uma entidade aprendida por máquina com subcomponentes em um exemplo de expressão em uma página de detalhes da intenção do portal do LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: diberry
ms.openlocfilehash: 58e813d30273db4e011039aa43cd59c61507895e
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74383709"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Rotular entidade aprendida por máquina em um exemplo de expressão

Rotular uma entidade em um exemplo expressão dá ao LUIS um exemplo do que é a entidade e onde a entidade pode aparecer no expressão. 

## <a name="labeling-machine-learned-entity"></a>Rotulando a entidade aprendida por máquina

Considere a frase `hi, please I want a cheese pizza in 20 minutes`. 

1. Selecione o texto mais à esquerda e, em seguida, selecione o texto mais à direita da entidade e, em seguida, escolha a entidade com a qual você deseja rotular, neste caso, ordem completa. A _ordem completa_ é rotulada na imagem a seguir.

    > [!div class="mx-imgBorder"]
    > Rótulo de ![completo de entidade aprendida por máquina](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Selecione a entidade na janela pop-up. A entidade rotulada de ordem de pizza completa inclui todas as palavras (da esquerda para a direita em inglês) que são rotuladas. 

## <a name="review-labeled-text"></a>Examinar texto rotulado

Após a rotulagem, examine o exemplo expressão e verifique se o intervalo selecionado de texto foi sublinhado com a entidade escolhida. A linha sólida indica que o texto foi rotulado. 

> [!div class="mx-imgBorder"]
> ![rotuladas como entidade conhecida pelo computador](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Confirmar entidade prevista

Se houver uma caixa de linha pontilhada ao longo do intervalo de texto e o nome da entidade estiver acima do expressão, isso indicará que o texto está previsto, mas _não rotulado ainda_. Para transformar a previsão em um rótulo, selecione a linha expressão e, em seguida, selecione **confirmar previsões de entidade**.

> [!div class="mx-imgBorder"]
> Previsão de ![](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png) entidade aprendida completa pelo computador

Como alternativa, você pode selecionar o nome da entidade acima do texto e, em seguida, selecionar **confirmar previsão** no menu que é exibido.

> [!div class="mx-imgBorder"]
> ![prever a entidade conhecida por computador com](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted-menu.png) de menu

## <a name="label-entity-by-painting-with-entity-palette-cursor"></a>Rotular entidade pintando com o cursor da paleta de entidades

A paleta de entidades oferece uma alternativa à experiência de rotulação anterior. Ele permite que você focalize o texto para rotulá-lo instantaneamente com uma entidade.

1. Abra a paleta de entidades selecionando o ícone de realce na parte superior direita da tabela expressão. 

    > [!div class="mx-imgBorder"]
    > ![paleta de entidades para entidade aprendida por máquina](media/label-utterances/example-1-label-machine-learned-entity-palette.png)

2. Selecione o componente de entidade. Essa ação é visualmente indicada com um novo cursor. O cursor segue o mouse à medida que você move no Portal. 

    > [!div class="mx-imgBorder"]
    > ![paleta de entidades para entidade aprendida por máquina](media/label-utterances/example-1-label-machine-learned-entity-palette-menu.png)

3. No exemplo expressão, _pinte_ a entidade com o cursor.

    > [!div class="mx-imgBorder"]
    > ![paleta de entidades para entidade aprendida por máquina](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="labeling-subcomponents-of-a-machine-learned-entity"></a>Rotulando subcomponentes de uma entidade aprendida por máquina

Subcomponentes em entidades são rotulados exatamente da mesma forma que as entidades de nível superior. Ao selecionar o texto, as entidades disponíveis na janela pop-up são relativas ao contexto no qual o texto é exibido. Por exemplo, se você tiver uma entidade aprendida por computador de cinco níveis e estiver selecionando um texto que tenha sido rotulado com o primeiro e o 2º nível (indicado por um nome de entidade rotulado no exemplo expressão), as entidades disponíveis na janela pop-up serão limitadas ao contexto de componentes do terceiro nível. Para rotular o texto com outras entidades, selecione **rótulo como outra opção de entidade** .

> [!div class="mx-imgBorder"]
> ![paleta de entidades para entidade aprendida por máquina](media/label-utterances/example-1-label-machine-learned-entity-subcomponent.png)

Os subcomponentes podem ser rotulados somente se o pai também for rotulado. 

## <a name="unlabel-entities"></a>Desrotular entidades

Para desrotular uma entidade, selecione o nome da entidade abaixo do texto e selecione **desrotular**. Se a entidade que você está tentando desrotular tiver rotulado subcomponentes, os subcomponentes deverão ser desrotulados primeiro. 

## <a name="editing-labels-using-the-entity-palette"></a>Editando rótulos usando a paleta de entidades

Se você cometer um erro ao rotular, a paleta de entidades é uma ferramenta fácil que permite edições rápidas. Por exemplo, se um rótulo de entidade abranger uma palavra extra por engano e já tiver rotulado subcomponentes, você poderá usar a paleta de entidade para enfatizar o menor intervalo de palavras necessário.

Por exemplo:

1. O subcomponente do tipo pizza se estende por "queijo pizza com", que inclui uma palavra incorreta extra--"com"

    > [!div class="mx-imgBorder"]
    > ![paleta de entidades para entidade aprendida por máquina](media/label-utterances/edit-label-with-palette-1.png)

2. Use a paleta de entidades para escolher o tipo de pizza e o pincel sobre "queijo pizza". O resultado é que apenas queijo pizza é rotulado como tipo de pizza agora.

    > [!div class="mx-imgBorder"]
    > ![paleta de entidades para entidade aprendida por máquina](media/label-utterances/edit-label-with-palette-2.png)


## <a name="labels-for-matching-text-entities"></a>Rótulos para entidades de texto correspondentes

As entidades de texto correspondentes incluem entidades predefinidas, entidades de expressão regular, entidades de lista e padrão. quaisquer entidades. Eles são rotulados automaticamente por LUIS para que não precisem ser rotulados manualmente pelos usuários.

## <a name="entity-prediction-errors"></a>Erros de previsão de entidade

Erros de previsão de entidade indicam que a entidade prevista não corresponde à entidade rotulada. Isso é visualizado com um indicador de cuidado ao lado do expressão.

> [!div class="mx-imgBorder"]
> ![paleta de entidades para entidade aprendida por máquina](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Passos seguintes

Use o [painel](luis-how-to-use-dashboard.md) e [examine o ponto de extremidade declarações](luis-how-to-review-endpoint-utterances.md) para melhorar a qualidade de previsão de seu aplicativo.