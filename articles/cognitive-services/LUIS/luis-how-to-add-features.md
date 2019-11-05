---
title: Listas de frases-LUIS
titleSuffix: Azure Cognitive Services
description: Use o Reconhecimento vocal (LUIS) para adicionar recursos de aplicativo que podem melhorar a detecção ou previsão de tentativas e entidades que são categorias e padrões
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: 0e3e4226eaaa0505eea96d8b3aca820f2327349e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467609"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>Usar listas de frases para impulsionar o sinal da lista de palavras

Você pode adicionar recursos ao seu aplicativo LUIS para melhorar sua precisão. Os recursos ajudam a LUIS fornecendo dicas de que determinadas palavras e frases fazem parte de um vocabulário de domínio de aplicativo. 

Uma [lista de frases](luis-concept-feature.md) inclui um grupo de valores (palavras ou frases) que pertencem à mesma classe e deve ser tratada da mesma forma (por exemplo, nomes de cidades ou produtos). O que a LUIS aprende sobre um deles também é aplicado automaticamente às outras pessoas. Essa lista não é a mesma coisa que uma [entidade de lista](reference-entity-list.md) (correspondências exatas de texto) de palavras correspondentes.

Uma lista de frases adiciona ao vocabulário do domínio de aplicativo como um segundo sinal para LUIS sobre essas palavras.

Examine os [conceitos de recurso](luis-concept-feature.md) para entender quando e por que usar uma lista de frases. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-phrase-list"></a>Adicionar lista de frases

LUIS permite até 10 listas de frases por aplicativo. 

1. Abra seu aplicativo clicando em seu nome na página **meus aplicativos** e, em seguida, clique em **Build**e, em seguida, clique em **listas de frases** no painel esquerdo do seu aplicativo. 

1. Na página **listas de frases** , clique em **criar nova lista de frases**. 
 
1. Na caixa de diálogo **Adicionar lista de frases** , digite `Cities` como o nome da lista de frases. Na caixa **valor** , digite os valores da lista de frases. Você pode digitar um valor por vez ou um conjunto de valores separados por vírgulas e pressionar **Enter**.

    ![Adicionar cidades da lista de frases](./media/luis-add-features/add-phrase-list-cities.png)

1. LUIS pode propor valores relacionados para adicionar à sua lista de frases. Clique em **recomendar** para obter um grupo de valores propostos que estão semanticamente relacionados aos valores adicionados. Você pode clicar em qualquer um dos valores propostos ou clicar em **Adicionar tudo** para adicionar todos eles.

    ![Valores de lista de frases propostas – adicionar tudo](./media/luis-add-features/related-values.png)

1. Clique **nesses valores como intercambiáveis** se os valores da lista de frases adicionados forem alternativas que podem ser usadas de forma intercambiável.

    ![Valores propostos da lista de frases – selecionar caixa intercambiável](./media/luis-add-features/interchangeable.png)

1. Clique em **Concluído**. A lista de frases "cidades" é adicionada à página de **listas de frases** .

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Você pode excluir ou desativar uma lista de frases da barra de ferramentas contextual na página **listas de frases** .

## <a name="next-steps"></a>Passos seguintes

Depois de adicionar, editar, excluir ou desativar uma lista de frases, [treine e teste o aplicativo](luis-interactive-test.md) novamente para ver se o desempenho melhora.
