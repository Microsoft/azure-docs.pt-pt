---
title: Descritores - LUIS
titleSuffix: Azure Cognitive Services
description: Use a Compreensão da Linguagem (LUIS) para adicionar funcionalidades de aplicações que possam melhorar a deteção ou previsão de intenções e entidades que categorias e padrões
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: diberry
ms.openlocfilehash: 7560fdcbfc77ea2655e8af641794478ead4c11c7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80631452"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>Use descritores para aumentar o sinal da lista de palavras

Pode adicionar funcionalidades à sua aplicação LUIS para melhorar a sua precisão. As funcionalidades ajudam o LUIS, fornecendo dicas de que certas palavras e frases fazem parte de um vocabulário de domínio de aplicação.

Um [descritor](luis-concept-feature.md) (lista de frases) inclui um grupo de valores (palavras ou frases) que pertencem à mesma classe e devem ser tratados da mesma forma (por exemplo, nomes de cidades ou produtos). O que o LUIS aprende sobre um deles é automaticamente aplicado aos outros também. Esta lista não é a mesma coisa que uma [entidade de lista](reference-entity-list.md) (correspondência sms exata) de palavras combinadas.

Um descritor adiciona ao vocabulário do domínio da aplicação como um segundo sinal para LUIS sobre essas palavras.

Reveja [os conceitos](luis-concept-feature.md) de recurso para entender quando e porquê usar um descritor.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Adicionar descritor

1. Abra a sua aplicação clicando no seu nome na página **das Minhas Apps** e, em seguida, clique em **Construir,** em seguida, clique em **Descriptors** no painel esquerdo da sua aplicação.

1. Na página **Desscriptors,** clique em **+ Adicionar Descritor**.

1. Na nova lista de **frases descritores,** introduza `Cities` um nome como para o descritor. Na caixa **Valor,** digite os valores dos `Seattle`descritores, tais como . Pode escrever um valor de cada vez, ou um conjunto de valores separados por vírgulas e, em seguida, pressionar **Enter**.

    > [!div class="mx-imgBorder"]
    > ![Adicionar cidades ddescritadoras](./media/luis-add-features/add-phrase-list-cities.png)

    Depois de ter introduzido valores suficientes para o LUIS, surgem sugestões. Pode **+ Adicionar todos os** valores propostos ou selecionar termos individuais.

1. Mantenha estes valores são verificados **intercambiáveis** se os valores do descritor adicionado são alternativas que podem ser utilizadas intercambiavelmente.

1. A lista de frases pode aplicar-se a toda a aplicação com a definição **Global,** ou a um modelo específico (intenção ou entidade). Se criar a lista de frases, como _descritor_ de uma intenção ou entidade, o alternância está definido para não ser global. Neste caso, o significado do alternância é que o descritor é uma característica apenas para esse modelo, portanto, _não global_ para a aplicação.

1. Selecione **Done** (Concluído). O novo descritor é adicionado à página **de Descritores.**

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Pode eliminar ou desativar um descritor da barra de ferramentas contextual na página **Desscriptors.**

## <a name="next-steps"></a>Passos seguintes

Depois de adicionar, editar, apagar ou desativar um descritor, [treinar e testar novamente a app](luis-interactive-test.md) para ver se o desempenho melhora.
