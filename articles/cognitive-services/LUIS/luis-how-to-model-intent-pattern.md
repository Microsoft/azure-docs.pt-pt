---
title: Padrões adicionam precisão - LUIS
titleSuffix: Azure Cognitive Services
description: Adicione modelos de padrão para melhorar a precisão da previsão nas aplicações de Compreensão da Linguagem (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 21afb12bf2464218119ebf52ebd980745e3d731d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76311721"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Como adicionar padrões para melhorar a precisão da previsão
Depois de uma aplicação LUIS receber expressões de ponto final, use um [padrão](luis-concept-patterns.md) para melhorar a precisão da previsão para as expressões que revelam um padrão na ordem de palavras e escolha de palavras. Os padrões utilizam [sintaxe](luis-concept-patterns.md#pattern-syntax) específica para indicar a localização de: [entidades,](luis-concept-entity-types.md) [funções](luis-concept-roles.md)de entidades e texto opcional.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> Os padrões apenas incluem pais de entidades aprendidas por máquinas, não subcomponentes.

## <a name="adding-example-utterances-as-pattern"></a>Adicionando pronunciações de exemplo como padrão

Se quiser adicionar um padrão para uma entidade, a maneira _mais fácil_ é criar o padrão a partir da página de detalhes de Intenções. Isto garante que a sua sintaxe corresponde à expressão do exemplo.

1. No [portal DE pré-visualização LUIS,](https://preview.luis.ai)selecione a aplicação na página **My Apps.**
1. Na página da lista **de Intenções,** selecione o nome de intenção da expressão de exemplo a partir da sua deseja criar uma expressão de modelo.
1. Na página de detalhes da Intenção, selecione a linha para o exemplo que pretende utilizar como expressão do modelo e, em seguida, selecione **+ Adicione como padrão** a partir da barra de ferramentas de contexto.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de selecionar a expressão do exemplo como um padrão de modelo na página de detalhes de Intenções.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. Na caixa pop-up, selecione **Done** na página de **padrões Confirmar.** Não é necessário definir os subcomponentes, constrangimentos ou descritores das entidades. Só precisas de listar a entidade aprendida com máquinas.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de confirmação de pronunciamento de exemplo como um padrão de modelo na página de detalhes de Intenções.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Se precisar de editar o modelo, como selecionar texto `[]` como opcional, com os suportes (quadrados), precisa de fazer esta edição a partir da página **Padrões.**

1. Na barra de navegação, selecione **Train** para treinar a app com o novo padrão.

## <a name="add-template-utterance-using-correct-syntax"></a>Adicione a expressão do modelo usando a sintaxe correta

1. Abra a sua aplicação selecionando o seu nome na página **My Apps** e, em seguida, selecione **Padrões** no painel esquerdo, sob o desempenho da **aplicação Improve**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot da lista de padrões](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Selecione a intenção correta para o padrão.

1. Na caixa de texto do modelo, escreva a expressão do modelo e selecione Enter. Quando pretender introduzir o nome da entidade, utilize a sintaxe da entidade de padrão correto. Inicie a sintaxe da entidade com `{`. A lista de entidades exibe. Selecione a entidade correta.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de entidade para padrão](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Se a sua entidade incluir um [papel](luis-concept-roles.md), `:`indique o papel com `{Location:Origin}`um único cólon, depois do nome da entidade, como . A lista de funções para as entidades exibe numa lista. Selecione a função e, em seguida, selecione Enter.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de entidade com papel](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Depois de selecionar a entidade correta, termine de introduzir o padrão e, em seguida, selecione Enter. Quando terminar de entrar em padrões, [treine](luis-how-to-train.md) a sua aplicação.

    > [!div class="mx-imgBorder"]
    > ![Screenshot do padrão introduzido com ambos os tipos de entidades](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Treine a sua aplicação depois de mudar de modelo com padrões
Depois de adicionar, editar, remover ou reatribuir um padrão, [treinar](luis-how-to-train.md) e [publicar](luis-how-to-publish-app.md) a sua aplicação para as suas alterações para afetar consultas de ponto final.

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

## <a name="use-contextual-toolbar"></a>Utilize barra de ferramentas contextual

A barra de ferramentas contextual acima da lista de padrões permite-lhe:

* Pesquisar padrões
* Editar um padrão
* Reatribuir o padrão individual a diferentes intenções
* Reatribuir vários padrões para diferentes intenções
* Eliminar-um-padrão único
* Eliminar vários padrões
* Lista de padrões de filtro por entidade
* Filtrar-padrão-lista-por-intenção
* Remover entidade ou filtro de intenção
* Adicione padrão da expressão existente na página de intenções ou entidades

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [construir um padrão](luis-tutorial-pattern.md) com um padrão.qualquer e papéis com um tutorial.
* Aprenda a [treinar](luis-how-to-train.md) a sua aplicação.
