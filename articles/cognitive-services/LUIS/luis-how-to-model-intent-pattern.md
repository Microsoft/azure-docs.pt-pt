---
title: Melhorar a precisão do padrões
titleSuffix: Language Understanding - Azure Cognitive Services
description: Saiba como adicionar modelos padrão em aplicativos de compreensão de idiomas (LUIS) para melhorar a exatidão da previsão.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: diberry
ms.openlocfilehash: 132f6eab86c02e28fe562a0c7d3357175e5813b8
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195052"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Como adicionar os padrões para melhorar a exatidão da previsão
Depois de uma aplicação do LUIS recebe expressões de ponto de extremidade, utilize um [padrão](luis-concept-patterns.md) para melhorar a exatidão da previsão para expressões que revelam um padrão na ordem das palavras e a escolha do word. Padrões de utilizam específico [sintaxe](luis-concept-patterns.md#pattern-syntax) para indicar a localização dos: [entidades](luis-concept-entity-types.md), funções de entidade e o texto opcional.

## <a name="add-template-utterance-to-create-pattern"></a>Adicionar a expressão de modelo para criar padrão
1. Abra a sua aplicação, selecionando o respetivo nome na **as minhas aplicações** página e, em seguida, selecione **padrões** no painel esquerdo, em **melhorar o desempenho da aplicação**.

    ![Captura de ecrã da lista de padrões](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Selecione a intenção correta para o padrão. 

    ![Selecione a intenção](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. Na caixa de texto modelo, escreva a expressão de modelo e selecione Enter. Quando pretender introduzir o nome da entidade, utilize a sintaxe de entidade padrão correto. Começar a sintaxe de entidade com `{`. A lista de entidades apresenta. Selecione a entidade correta e, em seguida, selecione Enter. 

    ![Captura de ecrã da entidade para padrão](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Se a sua entidade inclui uma função, indicar a função com dois pontos único `:`, após o nome de entidade, como `{Location:Origin}`. Apresenta a lista de funções para as entidades numa lista. Selecione a função e, em seguida, selecione Enter. 

    ![Captura de ecrã da entidade com a função](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Depois de selecionar a entidade correta, termina de introduzir o padrão e, em seguida, selecione Enter. Quando tiver terminado de padrões de digitação [treinar](luis-how-to-train.md) seu aplicativo.

    ![Captura de ecrã do padrão introduzido com ambos os tipos de entidades](./media/luis-how-to-model-intent-pattern/patterns-5.png)

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

## <a name="use-contextual-toolbar"></a>Utilize a barra de ferramentas contextual

A barra de ferramentas contextual acima da lista de padrões permite-lhe:

* Procure padrões
* Editar um padrão
* Reatribuir padrão individual em intenção diferente
* Reatribuir vários padrões em intenção diferente
* Eliminar um único padrão
* Eliminar vários padrões
* Filtrar a lista padrão por entidade
* Filtro-padrão-lista-por-intenção
* Remover entidade ou um filtro de intenção
* Adicionar o padrão de expressão existente na página de intenção ou a entidade

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [criar um padrão](luis-tutorial-pattern.md) com um pattern.any e funções com um tutorial.
* Saiba como [treinar](luis-how-to-train.md) seu aplicativo.
