---
title: Padrões-adição de precisão-LUIS
titleSuffix: Azure Cognitive Services
description: Adicione modelos de padrão para melhorar a precisão da previsão em aplicativos Reconhecimento vocal (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 39b56c5e73c8ce85a020402dafb622b90c536a1e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143816"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Como adicionar padrões para melhorar a precisão da previsão
Depois que um aplicativo LUIS recebe o ponto de extremidade declarações, use um [padrão](luis-concept-patterns.md) para melhorar a precisão da previsão para declarações que revela um padrão em ordem de palavras e escolha de palavra. Padrões usam [sintaxe](luis-concept-patterns.md#pattern-syntax) específica para indicar o local de: [entidades](luis-concept-entity-types.md), [funções](luis-concept-roles.md)de entidade e texto opcional.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-template-utterance-to-create-pattern"></a>Adicionar a expressão de modelo para criar padrão

1. Abra a sua aplicação, selecionando o respetivo nome na **as minhas aplicações** página e, em seguida, selecione **padrões** no painel esquerdo, em **melhorar o desempenho da aplicação**.

    > [!div class="mx-imgBorder"]
    > ![captura de tela da lista de padrões](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Selecione a intenção correta para o padrão. 

1. Na caixa de texto modelo, escreva a expressão de modelo e selecione Enter. Quando pretender introduzir o nome da entidade, utilize a sintaxe de entidade padrão correto. Começar a sintaxe de entidade com `{`. A lista de entidades apresenta. Selecione a entidade correta. 

    > [!div class="mx-imgBorder"]
    > Captura de tela de ![da entidade para o padrão](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Se sua entidade incluir uma [função](luis-concept-roles.md), indique a função com um único sinal de dois-pontos, `:`, após o nome da entidade, como `{Location:Origin}`. Apresenta a lista de funções para as entidades numa lista. Selecione a função e, em seguida, selecione Enter. 

    > [!div class="mx-imgBorder"]
    > ![captura de tela da entidade com a função](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Depois de selecionar a entidade correta, termina de introduzir o padrão e, em seguida, selecione Enter. Quando tiver terminado de padrões de digitação [treinar](luis-how-to-train.md) seu aplicativo.

    > [!div class="mx-imgBorder"]
    > ![captura de tela do padrão inserido com os dois tipos de entidades](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Preparar a sua aplicação depois de alterar o modelo com padrões
Depois de adicionar, editar, remover ou reatribuir um padrão [treinar](luis-how-to-train.md) e [publicar](luis-how-to-publish-app.md) as suas alterações afetar a consultas de ponto final na sua aplicação. 

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>Usar barra de ferramentas contextual

A barra de ferramentas contextual acima da lista de padrões permite que você:

* Pesquisar padrões
* Editar um padrão
* Reatribuir padrão individual em intenção diferente
* Reatribuir vários padrões em intenção diferente
* Excluir um padrão único
* Eliminar vários padrões
* Filtrar a lista padrão por entidade
* Filtro-padrão-listar por intenção
* Remover entidade ou um filtro de intenção
* Adicionar o padrão de expressão existente na página de intenção ou a entidade

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar um padrão](luis-tutorial-pattern.md) com um padrão. qualquer função e com um tutorial.
* Saiba como [treinar](luis-how-to-train.md) seu aplicativo.
