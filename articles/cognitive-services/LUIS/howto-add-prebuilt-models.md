---
title: Modelos pré-construídos para compreensão da linguagem
titleSuffix: Azure Cognitive Services
description: O LUIS inclui um conjunto de modelos pré-construídos para adicionar rapidamente cenários comuns e de utilizadores conversacionais.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: diberry
ms.openlocfilehash: 653882db4b62d7731123faf7b177da44dbd74e3f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584999"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Adicione modelos pré-construídos para cenários de utilização comuns

O LUIS inclui um conjunto de modelos pré-construídos para adicionar rapidamente cenários comuns e de utilizadores conversacionais. Esta é uma forma rápida e fácil de adicionar habilidades à sua aplicação de cliente conversacional sem ter que projetar os modelos para essas habilidades.

## <a name="add-a-prebuilt-domain"></a>Adicionar um domínio pré-concebido

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **De Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**

1. Selecione **domínios pré-construídos** a partir da barra de ferramentas esquerda.

1. Encontre o domínio que deseja adicionar à aplicação e, em seguida, selecione adicionar botão de **domínio.**

    > [!div class="mx-imgBorder"]
    > ![Adicionar domínio pré-construído do calendário](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Adicione uma intenção pré-construída

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **De Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**

1. Na página **Intenções,** selecione Adicionar intenção de **domínio pré-construído** a partir da barra de ferramentas acima da lista de intenções.

1. Selecione os **Utilitários.Cancele** a intenção do diálogo pop-up.

    > [!div class="mx-imgBorder"]
    > ![Adicionar intenção pré-construída](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Selecione o botão **Done.**

## <a name="add-a-prebuilt-entity"></a>Adicionar uma entidade pré-construída
1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **De Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. Selecione **Entidades** no lado esquerdo.

1. Na página **Entidades,** selecione **Adicionar entidade pré-construída.**

1. Em Adicionar caixa de diálogo de **entidades pré-construídas,** selecione a entidade pré-construída.

    > [!div class="mx-imgBorder"]
    > ![Adicionar caixa de diálogo de entidade pré-construída](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Selecione **Done** (Concluído). Depois de adicionada a entidade, não precisa de treinar a app.

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publicar para ver modelo pré-construído a partir do ponto final da previsão

A maneira mais fácil de ver o valor de um modelo pré-construído é consultar a partir do ponto final publicado.

## <a name="entities-containing-a-prebuilt-entity-token"></a>Entidades que contenham um símbolo de entidade pré-construída

Se tiver uma entidade aprendida por máquinas que necessite de uma característica necessária de uma entidade pré-construída, adicione um subcomponente à entidade aprendida pela máquina, adicione então uma característica _necessária_ de uma entidade pré-construída.

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Construir modelo a partir de .csv com APIs REST](./luis-tutorial-node-import-utterances-csv.md)
