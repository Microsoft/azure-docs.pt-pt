---
title: Os padrões adicionam precisão - LUIS
titleSuffix: Azure Cognitive Services
description: Adicione modelos de padrão para melhorar a precisão da previsão nas aplicações de compreensão de linguagem (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: c5e1cc5cdda921e63315c2904d560b7ad3939776
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95018859"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Como adicionar padrões para melhorar a precisão da previsão
Depois de uma aplicação LUIS receber expressões de ponto final, use um [padrão](luis-concept-patterns.md) para melhorar a precisão da previsão para expressões que revelam um padrão na ordem de palavras e na escolha de palavras. Os padrões utilizam [sintaxe](luis-concept-patterns.md#pattern-syntax) específica para indicar a localização de: [entidades,](luis-concept-entity-types.md) [funções](./luis-concept-entity-types.md)de entidade, e texto opcional.

> [!CAUTION]
> Os padrões incluem apenas pais de entidades de aprendizagem automática, não subentidades.

## <a name="add-template-utterance-using-correct-syntax"></a>Adicionar a expressão do modelo usando a sintaxe correta

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. Selecione **Padrões** no painel esquerdo, em Melhorar o **desempenho da aplicação**.

1. Selecione a intenção correta para o padrão.

1. Na caixa de texto do modelo, digite a expressão do modelo e selecione Enter. Quando pretender introduzir o nome da entidade, utilize a sintaxe da entidade de padrão correta. Inicie a sintaxe da entidade com `{` . A lista de entidades exibe. Selecione a entidade correta.

    > [!div class="mx-imgBorder"]
    > ![Screenshot da entidade para padrão](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Se a sua entidade incluir uma [função,](./luis-concept-entity-types.md)indique o papel com um único `:` cólon, após o nome da entidade, como `{Location:Origin}` . A lista de funções para as entidades apresenta-se numa lista. Selecione a função e, em seguida, selecione Enter.

    > [!div class="mx-imgBorder"]
    > ![Screenshot da entidade com papel](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Depois de selecionar a entidade correta, termine de introduzir o padrão e, em seguida, selecione Enter. Quando terminar de introduzir padrões, [treine](luis-how-to-train.md) a sua aplicação.

    > [!div class="mx-imgBorder"]
    > ![Screenshot do padrão introduzido com ambos os tipos de entidades](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="create-a-patternany-entity"></a>Criar um padrão.qualquer entidade

[Padrão.qualquer](luis-concept-entity-types.md) entidade é válida apenas em [padrões,](luis-how-to-model-intent-pattern.md)não em declarações de exemplo. Este tipo de entidade ajuda a LUIS a encontrar o fim de entidades de comprimento variado e escolha de palavras. Como esta entidade é usada num padrão, a LUIS sabe onde está o fim da entidade no modelo de expressão.

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. A partir da secção **Construir,** selecione **Entidades** no painel esquerdo e, em seguida, selecione **+ Criar**.

1. Na caixa de diálogo **tipo entidade,** introduza o nome da entidade na caixa **Nome** e selecione **Padrão.Qualquer** como o **Tipo** selecione **Criar**.

    Uma vez que você [cria uma expressão de padrão](luis-how-to-model-intent-pattern.md) usando esta entidade, a entidade é extraída com um algoritmo combinado de aprendizagem automática e correspondência de texto.

## <a name="adding-example-utterances-as-pattern"></a>Adicionar palavras de exemplo como padrão

Se quiser adicionar um padrão para uma entidade, a maneira _mais fácil_ é criar o padrão a partir da página de detalhes intenção. Isto garante que a sintaxe corresponde à expressão de exemplo.

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **de Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. Na página da lista **De Intenções,** selecione o nome de intenção da expressão de exemplo a partir do que pretende criar uma expressão de modelo.
1. Na página de detalhes do Intenção, selecione a linha para a expressão de exemplo que pretende usar como expressão do modelo e, em seguida, selecione **+ Adicione como padrão** da barra de ferramentas de contexto.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de selecionar a expressão de exemplo como um padrão de modelo na página de detalhes de Intenção.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

    A expressão deve incluir uma entidade para criar um padrão a partir da expressão.

1. Na caixa pop-up, selecione **Feito** na página **'Confirmar padrões'.** Não é necessário definir as subentuidades ou características das entidades. Só precisa de listar a entidade de aprendizagem automática.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de confirmar a expressão do exemplo como um padrão de modelo na página de detalhes de Intenção.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Se precisar de editar o modelo, tal como selecionar texto como opcional, com os `[]` suportes (quadrados), tem de fazer esta edição a partir da página **Padrões.**

1. Na barra de navegação, selecione **Train** para treinar a app com o novo padrão.

## <a name="train-your-app-after-changing-model-with-patterns"></a>Treine a sua app depois de mudar de modelo com padrões
Depois de adicionar, editar, remover ou reatribuir um padrão, [treine](luis-how-to-train.md) e [publique](luis-how-to-publish-app.md) a sua aplicação para as suas alterações para afetar as consultas de ponto final.

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

## <a name="use-contextual-toolbar"></a>Use a barra de ferramentas contextual

A barra de ferramentas contextual acima da lista de padrões permite-lhe:

* Pesquisa de padrões
* Editar um padrão
* Reatribuir o padrão individual para diferentes intenções
* Reatribuir vários padrões para diferentes intenções
* Excluir-a-um-único-padrão
* Eliminar vários padrões
* Lista de padrões de filtro por entidade
* Filtrar padrão-lista-por-intenção
* Remover entidade ou filtro de intenção
* Adicione o padrão da expressão existente na página de intenção ou entidade

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [construir um padrão](luis-tutorial-pattern.md) com um padrão.qualquer e papéis com um tutorial.
* Aprenda a [treinar](luis-how-to-train.md) a sua aplicação.
