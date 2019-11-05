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
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: cbd8ad73ff4a03790dd6b22d5ce33acf09a2b125
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491362"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Como adicionar padrões para melhorar a precisão da previsão
Depois que um aplicativo LUIS recebe o ponto de extremidade declarações, use um [padrão](luis-concept-patterns.md) para melhorar a precisão da previsão para declarações que revela um padrão em ordem de palavras e escolha de palavra. Padrões usam [sintaxe](luis-concept-patterns.md#pattern-syntax) específica para indicar o local de: [entidades](luis-concept-entity-types.md), [funções](luis-concept-roles.md)de entidade e texto opcional.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-template-utterance-to-create-pattern"></a>Adicionar modelo expressão para criar padrão
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** e, em seguida, selecione **padrões** no painel esquerdo, em melhorar o **desempenho do aplicativo**.

    ![Captura de tela da lista de padrões](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Selecione a intenção correta para o padrão. 

    ![Selecionar intenção](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. Na caixa de texto modelo, digite o modelo expressão e selecione Enter. Quando você quiser inserir o nome da entidade, use a sintaxe de entidade de padrão correta. Inicie a sintaxe da entidade com `{`. A lista de entidades é exibida. Selecione a entidade correta e, em seguida, selecione Enter. 

    ![Captura de tela de entidade para padrão](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Se sua entidade incluir uma [função](luis-concept-roles.md), indique a função com um único sinal de dois-pontos, `:`, após o nome da entidade, como `{Location:Origin}`. A lista de funções para as entidades é exibida em uma lista. Selecione a função e, em seguida, selecione Enter. 

    ![Captura de tela da entidade com a função](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Depois de selecionar a entidade correta, conclua a inserção do padrão e, em seguida, selecione Enter. Quando você terminar de inserir padrões, [treine](luis-how-to-train.md) seu aplicativo.

    ![Captura de tela do padrão inserido com ambos os tipos de entidades](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Treinar seu aplicativo depois de alterar o modelo com padrões
Depois de adicionar, editar, remover ou reatribuir um padrão, [treine](luis-how-to-train.md) e [publique](luis-how-to-publish-app.md) seu aplicativo para que suas alterações afetem as consultas de ponto de extremidade. 

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
* Reatribuir o padrão individual a uma intenção diferente
* Reatribuir vários padrões a uma intenção diferente
* Excluir um padrão único
* Excluir vários padrões
* Lista de padrões de filtro por entidade
* Filtro-padrão-listar por intenção
* Remover filtro de entidade ou intenção
* Adicionar padrão de expressão existente na página de intenção ou entidade

## <a name="next-steps"></a>Passos seguintes

* Saiba como [criar um padrão](luis-tutorial-pattern.md) com um padrão. qualquer função e com um tutorial.
* Saiba como [treinar](luis-how-to-train.md) seu aplicativo.
