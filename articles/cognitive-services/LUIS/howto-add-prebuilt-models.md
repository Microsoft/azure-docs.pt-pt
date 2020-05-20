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
ms.date: 05/17/2020
ms.author: diberry
ms.openlocfilehash: abf149d621d283744a71e08ea4ac2199a1994d57
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680947"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Adicione modelos pré-construídos para cenários de utilização comuns

O LUIS inclui um conjunto de modelos pré-construídos para adicionar rapidamente cenários comuns e de utilizadores conversacionais. Esta é uma forma rápida e fácil de adicionar habilidades à sua aplicação de cliente conversacional sem ter que projetar os modelos para essas habilidades.

## <a name="add-a-prebuilt-domain"></a>Adicionar um domínio pré-concebido

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **De Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**

1. Selecione **Domínios Pré-construídos** a partir da barra de ferramentas esquerda.

1. Encontre o domínio que deseja adicionar à aplicação e, em seguida, selecione adicionar botão de **domínio.**

    > [!div class="mx-imgBorder"]
    > ![Adicionar domínio pré-construído do calendário](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Adicione uma intenção pré-construída

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **De Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**

1. Na página **Intenções,** selecione Adicionar intenção de **domínio pré-construído** a partir da barra de ferramentas acima da lista de intenções.

1. Selecione uma intenção a partir do diálogo pop-up.

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

## <a name="add-a-prebuilt-domain-entity"></a>Adicione uma entidade de domínio pré-construída
1. Inscreva-se no [portal LUIS](https://www.luis.ai)e selecione o seu recurso **De Subscrição** e **Autoria** para ver as aplicações atribuídas a esse recurso de autoria.
1. Abra a sua aplicação selecionando o seu nome na página **My Apps.**
1. Selecione **Entidades** no lado esquerdo.

1. Na página **Entidades,** selecione Adicionar entidade de **domínio pré-construído.**

1. Em Adicionar caixa de diálogo de modelos de **domínio pré-construído,** selecione a entidade de domínio pré-construída.

1. Selecione **Done** (Concluído). Depois de adicionada a entidade, não precisa de treinar a app.

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publicar para ver modelo pré-construído a partir do ponto final da previsão

A maneira mais fácil de ver o valor de um modelo pré-construído é consultar a partir do ponto final publicado.

## <a name="entities-containing-a-prebuilt-entity-token"></a>Entidades que contenham um símbolo de entidade pré-construída

Se tiver uma entidade de aprendizagem automática que necessite de uma característica necessária de uma entidade pré-construída, adicione uma subentidade à entidade de aprendizagem automática e, em seguida, adicione uma característica _necessária_ de uma entidade pré-construída.

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Construir modelo a partir de .csv com APIs REST](./luis-tutorial-node-import-utterances-csv.md)
