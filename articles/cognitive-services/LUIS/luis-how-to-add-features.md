---
title: Descritores-LUIS
titleSuffix: Azure Cognitive Services
description: Utilizar a compreensão de idiomas (LUIS) para adicionar funcionalidades de aplicação que podem melhorar a deteção ou a predição de objetivos e entidades que categorias e padrões
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: diberry
ms.openlocfilehash: 2b5046bb61dcafbba0b0540935e08777fbd747a5
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123149"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>Usar descritores para impulsionar o sinal da lista de palavras

Pode adicionar funcionalidades à sua aplicação LUIS para melhorar a precisão. Recursos ajudam o LUIS, fornecendo as sugestões que determinadas palavras e frases fazem parte de um vocabulário de domínio de aplicação. 

Um [descritor](luis-concept-feature.md) (lista de frases) inclui um grupo de valores (palavras ou frases) que pertencem à mesma classe e deve ser tratado da mesma forma (por exemplo, nomes de cidades ou produtos). O que LUIS aprende sobre uma delas é aplicada automaticamente para os outros também. Essa lista não é a mesma coisa que uma [entidade de lista](reference-entity-list.md) (correspondências exatas de texto) de palavras correspondentes.

Um descritor adiciona ao vocabulário do domínio do aplicativo como um segundo sinal para LUIS sobre essas palavras.

Examine os [conceitos de recurso](luis-concept-feature.md) para entender quando e por que usar um descritor. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Adicionar descritor

1. Abra seu aplicativo clicando em seu nome na página **meus aplicativos** e, em seguida, clique em **criar**e em **descritores** no painel esquerdo do seu aplicativo. 

1. Na página dos **descritores** , clique em **+ Adicionar descritor**. 
 
1. Na caixa de diálogo **criar novo descritor de lista de frases** , insira um nome como `Cities` para o descritor. Na caixa **valor** , digite os valores dos descritores, como `Seattle`. Escreva um valor numa hora ou um conjunto de valores separados por vírgulas e, em seguida, prima **Enter**.

    > [!div class="mx-imgBorder"]
    > ![adicionar cidades de descritor](./media/luis-add-features/add-phrase-list-cities.png)

    Depois de inserir valores suficientes para LUIS, serão exibidas sugestões. Você pode **+ adicionar todos** os valores propostos ou selecionar termos individuais.

1. Mantenha **esses valores intercambiáveis** verificados se os valores do descritor adicionados forem alternativas que podem ser usadas de forma intercambiável.

1. Selecione **Done** (Concluído). O novo descritor é adicionado à página dos **descritores** .

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Você pode excluir ou desativar um descritor da barra de ferramentas contextual na página **descritores** .

## <a name="next-steps"></a>Passos seguintes

Depois de adicionar, editar, excluir ou desativar um descritor, [treine e teste o aplicativo](luis-interactive-test.md) novamente para ver se o desempenho melhora.
